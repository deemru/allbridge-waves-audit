# allbridge-waves security audit

- [allbridge-waves security audit](#allbridge-waves-security-audit)
  - [Survey scope](#survey-scope)
  - [Project composition](#project-composition)
  - [Security](#security)
    - [General](#general)
    - [Administration Policy](#administration-policy)
  - [Severity Levels](#severity-levels)
  - [Vulnerability summary](#vulnerability-summary)
  - [Vulnerabilities appendix](#vulnerabilities-appendix)
    - [Unrestricted initialization](#unrestricted-initialization)
    - [Reinitialization](#reinitialization)
    - [Unauthorized permissions](#unauthorized-permissions)
    - [Overflows \& underflows](#overflows--underflows)
    - [Reentrancy](#reentrancy)
    - [Unauthorized fund transfer](#unauthorized-fund-transfer)
    - [Token lockout](#token-lockout)
    - [Unauthorized contract termination](#unauthorized-contract-termination)
    - [Unexpected halt](#unexpected-halt)
    - [External call dependency](#external-call-dependency)
    - [Deterministic randomness](#deterministic-randomness)
    - [Conflicting state updates](#conflicting-state-updates)
    - [Complexity overflow](#complexity-overflow)
    - [Complexity optimization](#complexity-optimization)
    - [Incorrect payment](#incorrect-payment)
    - [Zero payment](#zero-payment)
    - [Admin input validation](#admin-input-validation)
    - [Input validation](#input-validation)
    - [Serialization](#serialization)
    - [Logic flaws](#logic-flaws)
    - [Abusive front-running](#abusive-front-running)
    - [Front-running](#front-running)
    - [Timestamp manipulation](#timestamp-manipulation)
    - [Insider attack](#insider-attack)
    - [Upgradeability](#upgradeability)

## Survey scope

The [allbridge-waves-contract](https://github.com/WavesBridge/allbridge-waves-contract) project was examined up to and including June 14, 2022.

At the time of examination, the project included an overall codebase with two Waves smart contracts and the immediate objective of this survey is to identify and address security improvements on the Waves side, while acknowledging the need for consistent architecture across all chains.

## Project composition

The composition of the project is derived from the publicly available information on the [allbridge-waves-contract](https://github.com/WavesBridge/allbridge-waves-contract) repository and additional details from the development team, including the source codes of the smart contracts and their deployment addresses on the Waves network.

The final set of contracts is:
- `bridge`: `3PQDRn2XPVtctY2pk83W3qVykWqXPvoU5Zp`
- `validator`: `3PCNVHWCxkp47QhpaVwkCUvH7uKt4dvsefj`

## Security

### General

The `allbridge-waves` system consists of two Waves smart contracts, `bridge` and `validator`. The `validator` contract maintains a database of cross-chain transfers without offering any public functions. The `bridge` contract relies on `validator` for data integrity and provides user-facing functions that handle operations between Waves and external blockchains.

In this audit, the `allbridge-waves` smart contracts are examined for vulnerabilities that could negatively affect the protocol, including both direct fund losses and unexpected outcomes of the calculations and formulas used. Because such threats manifest through on-chain transactions, the analysis focuses on typical system users who can successfully invoke at least one of the contracts’ functions. Compromised private keys or super-administrators are outside the current scope.

Transactions on the Waves network can modify these contracts only by changing their state, typically through writing a new key value or transferring tokens. Consequently, the audit begins by identifying the functions capable of performing these state-altering actions, then determining which user-invocable functions trigger those actions. The resulting overview confirms that the functionality accessible to users aligns with the protocol’s declared behavior. Service calls that rely on contract interactions verify that callers belong to recognized system groups, matching the intended logic.

### Administration Policy

The `allbridge-waves` contracts operate under the default global administration policy inherent to the Waves network. This means they rely on the standard signature verification (public key checks) provided by the blockchain itself, without adding any custom authorization logic at the contract level. Initialization occurs once, setting an administrator or manager who is then authorized to perform privileged actions.

While this design presents no direct implementation flaws, it remains a straightforward administration model. Compromise of the initially assigned private keys for the `bridge` or `validator` contracts could pose notable security risks, as there is no additional mechanism (such as a multisig) to mitigate unauthorized actions once the contract key is exposed.

## Severity Levels

It’s important to note that severity levels may vary depending on the specific context and system being assessed, and different organizations may have their own definitions or classifications for severity levels. It’s crucial to thoroughly understand and follow the severity levels specified in the specific technical report or security assessment being referenced.

- **TRIVIAL**: Refers to findings that have minimal impact on the system or application's security and are of negligible concern. These issues are typically inconsequential and do not pose any significant risk to overall security.

- **LOW**: Refers to findings that have minor impact on the system or application's security and may not require immediate action. These issues are typically of limited concern and do not represent a critical threat to overall security.

- **MEDIUM**: Refers to findings that have a moderate impact on the system or application's security and may require attention in the near future. While not critical or urgent, they carry potential risk that should not be overlooked.

- **HIGH**: Refers to findings that have a significant impact on the system or application's security and may involve vulnerabilities not openly accessible to everyone or requiring special conditions, privileges, or knowledge to exploit. These issues pose a substantial threat and need to be addressed promptly.

- **CRITICAL**: Refers to findings that have a severe impact on the system or application's security and can be directly and publicly exploited by malicious actors without special privileges. These issues pose an immediate and critical risk to the overall security of the system and must be resolved urgently to prevent severe consequences.

## Vulnerability summary

| Severity   | Count | Fixed |
|------------|-------|-------|
| TRIVIAL    | 0     | 0     |
| LOW        | 2     | 1     |
| MEDIUM     | 2     | 1     |
| HIGH       | 0     | 0     |
| CRITICAL   | 0     | 0     |

- Total found: 4
- Total fixed: 2

Not-fixed vulnerabilities are considered Accepted under the `allbridge-waves` broader design where they are mitigated, ensuring consistency across the entire framework.

## Vulnerabilities appendix

### Unrestricted initialization
- **Description**: Whether a smart contract's constructor can be called by anyone.
- **Severity**: MEDIUM
- **Result**: Fixed

### Reinitialization
- **Description**: Whether a smart contract's constructor can be called multiple times, allowing an attacker to reset the contract's state or parameters, potentially leading to unexpected behavior or loss of funds.
- **Severity**: CRITICAL
- **Result**: Not found

### Unauthorized permissions
- **Description**: Whether an unauthorized user can gain administrative or safeguard permissions in a smart contract, potentially allowing them to manipulate contract parameters, modify critical state variables, or execute privileged functions without proper authorization.
- **Severity**: CRITICAL
- **Result**: Not found

### Overflows & underflows
- **Description**: Whether a smart contract is susceptible to general overflow or underflow vulnerabilities, where arithmetic operations on contract variables may result in unexpected behavior or errors due to integer overflow or underflow.
- **Severity**: LOW
- **Result**: Fixed

### Reentrancy
- **Description**: Whether a code of a smart contract can call back into the same contract and modify its state, potentially leading to unexpected changes in contract behavior or manipulation of critical state variables.
- **Severity**: CRITICAL
- **Result**: Not found

### Unauthorized fund transfer
- **Description**: Whether a smart contract returning funds to an arbitrary or unverified address, potentially allowing an attacker to redirect funds to an unauthorized address.
- **Severity**: CRITICAL
- **Result**: Not found

### Token lockout
- **Description**: Whether a smart contract locks tokens indefinitely, preventing their transfer or use beyond a certain point in time, which may result in loss of access or utility for token holders.
- **Severity**: MEDIUM
- **Result**: Not found

### Unauthorized contract termination
- **Description**: Whether a smart contract is susceptible to being killed or terminated by an arbitrary party, resulting in the contract being rendered inactive or non-functional.
- **Severity**: HIGH
- **Result**: Not found

### Unexpected halt
- **Description**: Whether a smart contract is vulnerable to a Denial-of-Service (DoS) attack due to unexpected or erroneous usage, which can result in contract halting.
- **Severity**: MEDIUM
- **Result**: Not found

### External call dependency
- **Description**: Whether a smart contract relies on the return value of an external call, which can be manipulated by an attacker, potentially leading to unexpected or malicious behavior.
- **Severity**: MEDIUM
- **Result**: Not found

### Deterministic randomness
- **Description**: Whether a smart contract contains a predictable randomness source, which can be exploited by an attacker to predict the outcome of randomness-dependent operations.
- **Severity**: MEDIUM
- **Result**: Not found

### Conflicting state updates
- **Description**: Whether a smart contract function's data writes have overlaps instead of aggregations, resulting in potential conflicts or inconsistent state updates.
- **Severity**: MEDIUM
- **Result**: Not found

### Complexity overflow
- **Description**: Whether smart contracts have scenarios that are not possible under certain conditions due to complexity limits, potentially leading to unexpected behavior or limitations in contract functionality.
- **Severity**: MEDIUM
- **Result**: Not found

### Complexity optimization
- **Description**: Whether a smart contract has excess complexity usage, which can lead to scalability issues or limitations in future functionality.
- **Severity**: LOW
- **Result**: Not found

### Incorrect payment
- **Description**: Whether a smart contract can accept a different payment asset than the one required, potentially resulting in incorrect value assessment or improper handling of payments.
- **Severity**: HIGH
- **Result**: Not found

### Zero payment
- **Description**: Whether invoking a smart contract with an attached amount of 0 for an asset renders the invocation impossible or leads to unexpected behavior.
- **Severity**: MEDIUM
- **Result**: Not found

### Admin input validation
- **Description**: Whether admin inputs are not properly validated, resulting in potential unexpected behavior or exploitation of the system.
- **Severity**: LOW
- **Result**: Accepted

### Input validation
- **Description**: Whether user inputs are not properly validated, resulting in potential unexpected behavior or exploitation of the system.
- **Severity**: CRITICAL
- **Result**: Not found

### Serialization
- **Description**: Whether a smart contract uses serialization data that can be encoded non-deterministically, resulting in the movement of fields inside the serialized data.
- **Severity**: HIGH
- **Result**: Not found

### Logic flaws
- **Description**: Whether issues with the logic or flow of the contract may lead to unintended behavior or exploitation.
- **Severity**: CRITICAL
- **Result**: Not found

### Abusive front-running
- **Description**: Whether smart contracts are susceptible to front-running attacks using the same arguments, potentially invalidating the original transaction of an honest user.
- **Severity**: MEDIUM
- **Result**: Accepted

### Front-running
- **Description**: Whether smart contracts are susceptible to front-running attacks, where an attacker can manipulate the order of transactions or exploit the order of execution in the Waves blockchain to gain an unfair advantage or manipulate outcomes.
- **Severity**: CRITICAL
- **Result**: Not found

### Timestamp manipulation
- **Description**: Whether timestamps or block numbers are used for time-based logic and can be manipulated, leading to potential exploits.
- **Severity**: MEDIUM
- **Result**: Not found

### Insider attack
- **Description**: Whether individuals or entities involved in the development, deployment, or maintenance of the contract may exploit their access and permissions.
- **Severity**: CRITICAL
- **Result**: Not found

### Upgradeability
- **Description**: Whether smart contracts are immutable, making it challenging to fix discovered issues or vulnerabilities.
- **Severity**: HIGH
- **Result**: Not found
