# vSQL: Verifying Arbitrary SQL Queries over Dynamic Outsourced Databases

A technical research presentation exploring **vSQL**, a cryptographic protocol for verifying the correctness of SQL query results returned by untrusted servers while keeping the client's local storage and verification work small.

**Course:** Cryptographic Protocols — CUNY  
**Presentation author:** Angel Coronel  
**Presentation date:** December 18, 2025  
**Project type:** Research paper review and technical presentation (not a vSQL implementation)

## Overview

Outsourcing databases to cloud infrastructure reduces local storage and maintenance costs, but raises a question: **How can a resource-constrained client check that an untrusted server answered a SQL query correctly without downloading the entire database or running the query again?**

The research paper *vSQL: Verifying Arbitrary SQL Queries over Dynamic Outsourced Databases* proposes a verifiable database protocol combining interactive proofs with polynomial delegation. A client retains a compact commitment to its outsourced data, while the server computes queries and participates in a proof protocol that the client can verify.

This repository documents my course presentation explaining the protocol, its cryptographic building blocks, SQL-to-circuit translation, security goals, and performance trade-offs.

## Topics covered

- **Outsourced database verification:** The client–server trust model, commitments, succinct verification, and dynamic updates.
- **CMT interactive proofs:** Verifying layered arithmetic circuit evaluations by reducing claims to polynomial identities checked at random points.
- **Multilinear extensions (MLEs):** Expressing database data as low-degree multivariate polynomials suitable for sum-check-based techniques.
- **Verifiable polynomial delegation:** Checking server-provided polynomial evaluations against a commitment, including pairing-based techniques.
- **vSQL workflow:** Setup, commitment, query execution, proof generation, verification, and authenticated updates.
- **SQL to arithmetic circuits:** Representations of filters (`WHERE`), aggregates (`SUM`/`COUNT`), joins, and `GROUP BY`, including auxiliary witness strategies.
- **Security and performance:** Soundness assumptions, prover overhead, expressive query support, and comparisons reported by the original researchers.

## High-level protocol

1. **Setup and commit:** The database owner creates public parameters and a cryptographic commitment to the database.
2. **Query:** The client sends an SQL query to the server that stores the database.
3. **Compute and prove:** The server evaluates the query and participates in a proof of correctness for the associated arithmetic circuit.
4. **Verify:** The client verifies both the computation and the committed input evaluations, accepting or rejecting the result.
5. **Update:** For database modifications, the client verifies the update and adopts a corresponding new commitment.

The protocol relies on **CMT to verify the computation** and **polynomial delegation to authenticate data-dependent evaluations**.

## Worked example

The presentation uses a small `Orders` table to illustrate filtering and aggregation:

```sql
SELECT SUM(price)
FROM Orders
WHERE status = 'PAID';
```

For prices `10`, `7`, `5`, and `2`, and statuses `PAID`, `PENDING`, `PAID`, and `PAID`, the filter mask is `[1, 0, 1, 1]` and the expected sum is **17**. The presentation then explains how an arithmetic circuit encodes this calculation and how commitments and interactive proof checks support verification without retaining the full table at the client.

*This is a conceptual walkthrough; it is not an executable cryptographic proof or a software implementation.*

## Main takeaways

- vSQL supports expressive database queries, including updates, by representing their computations as arithmetic circuits.
- The verifier can retain a small authenticated digest rather than the entire database.
- The prover performs significantly more work than an ordinary SQL server, creating a performance-versus-integrity trade-off.
- The original research reports improvements in prover time against the **SNARK-based systems evaluated in that paper**; these are historical experimental results, **not benchmarks performed in this course project**.
- Efficient compilation of complex SQL operations and witness generation remain engineering challenges.

## Reference

Yupeng Zhang, Daniel Genkin, Jonathan Katz, Dimitrios Papadopoulos, and Charalampos Papamanthou. **“vSQL: Verifying Arbitrary SQL Queries over Dynamic Outsourced Databases.”** *IEEE Symposium on Security and Privacy*, 2017.

This project is an independent educational presentation about the authors' research, not an original implementation, experimental reproduction, or contribution to the vSQL protocol.

## Author

**Angel Coronel**  
Cryptographic Protocols — CUNY  
December 2025
