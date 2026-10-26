# Distributed Multi-User Banking Transaction Engine (Capstone - Option 2)

Java 17 - sockets - ExecutorService - ReentrantLock - JDBC (H2) - Strategy + Observer (+ Singleton, Factory-style registry).

## Requirement -> where it is implemented
| Requirement | Implementation |
|---|---|
| Safe balancing under concurrent sessions (locks/atomics) | `BankService` - fair `ReentrantLock` per account, ordered acquisition for transfers (no deadlock), `tryLock` timeout; `AtomicLong` metrics |
| Socket client-server, multi-client, authentication | `BankServer` (accept loop + fixed `ExecutorService`), `ClientHandler` (one per session), `AuthService` + PBKDF2 `PasswordHasher` |
| JDBC ledger with automated rollback | `Database.transaction()` commits on success / `rollback()` on any exception; `LedgerDao`, `AccountDao` use `PreparedStatement`; failed attempts audited as `FAILED` rows |
| Strategy - dynamic fees by account type | `FeeStrategy` + `Standard/Premium/BusinessFeeStrategy`, selected by `FeeStrategyFactory` from the account's `AccountType` |
| Observer - alerts on high-value transactions | `TransactionPublisher` (subject), `ConsoleAlertObserver`, `EmailAlertObserver` (simulated outbox file) |

Extra design notes: `Database` is a thread-safe Singleton owning a bounded connection pool; DB `CHECK (balance >= 0)` is a last-line backstop; observers fire only after commit.

## Build & run
```
mvn clean package            # runs the JUnit suite, builds target/bank-engine.jar
java -cp target/bank-engine.jar com.bank.server.BankServerMain 9090 16 10000
java -cp target/bank-engine.jar com.bank.client.BankClient localhost 9090
java -cp target/bank-engine.jar com.bank.client.LoadTestClient localhost 9090 20 25
```
Data persists in `./data/bank*.db`; delete `data/` for a clean database. E-mail alerts land in `outbox/high-value-alerts.log`.

## Desktop client (Swing GUI)
```
java -cp target/bank-engine.jar com.bank.client.BankGui localhost 9090
```
In IntelliJ: run `BankGui.main`. Start the server first. Use *Register & log in* for a new user, or *Log in*. The GUI uses the same protocol as `BankClient`; the server is unchanged.

## Protocol (text lines; replies start `OK` / `ERR`)
`REGISTER user pass STANDARD|PREMIUM|BUSINESS` - `LOGIN user pass` - `DEPOSIT amt` - `WITHDRAW amt` -
`TRANSFER toAccount amt` - `BALANCE` - `STATEMENT [n]` (ends with `END`) - `STATS` - `LOGOUT`

## Live demo script (about 5 minutes)
1. Start the server (threshold 10000). Open two client terminals.
2. T1: `REGISTER alice pw1234 STANDARD`, `LOGIN alice pw1234`, `DEPOSIT 20000` -> red **HIGH-VALUE ALERT** on the server console, and an entry in the outbox log (Observer).
3. T2: `REGISTER bob pw1234 PREMIUM` (note account numbers), `LOGIN`.
4. T1: `WITHDRAW 100` -> fee 1.50. T2: `DEPOSIT 500`, `WITHDRAW 100` -> fee 0.50 (Strategy: same action, different fee).
5. T1: `TRANSFER <bob's account> 250`, then `STATEMENT` on both: matching ledger rows.
6. T1: `WITHDRAW 999999` -> `ERR Insufficient funds`; `STATEMENT` shows a `FAILED` audit row; balance unchanged.
7. Concurrency proof: run `LoadTestClient` (20 connections x 25 ops on one account) -> both phases print `PASS`.
8. Rollback proof: `mvn test -Dtest=BankServiceTest#failureMidTransferRollsBackEverything` (a crash is injected after the debit; both balances are unchanged).
