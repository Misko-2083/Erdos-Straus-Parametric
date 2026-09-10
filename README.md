# Parametric (g, u, v)-Reduction and Hyperbolic Construction for the Erdős–Straus Conjecture

**Research note · September 2026**

This repository contains an elementary parametric family of solutions to the Erdős–Straus equation

\[
\frac{4}{n} = \frac{1}{x} + \frac{1}{y} + \frac{1}{z}
\]

indexed by integers \((k,g,u,v)\). The search for \((y,z)\) with fixed \(x\) reduces to finding an integer point on the hyperbola

\[
(4gu-1)(4gv-1) = 4gkn + 1.
\]

### Key points
- The construction is fully explicit and algebraically derived from first principles.
- It covers **100 %** of the six “difficult” residue classes modulo 840 up to \(n = 10^6\), and > 99.99 % up to \(10^7\) (the few exceptions are resolved by a modestly larger search range).
- **This does not prove the conjecture.** There is no proven upper bound on the parameters in terms of \(n\), and the Mordell–Schinzel theorem shows that no finite polynomial identity can cover the quadratic-residue classes.

Full mathematical development (in Serbian) is in [`paper/erdos_straus_hiperbola.md`](paper/erdos_straus_hiperbola.md).

English version 
[`paper/erdos_straus_hiperbola_english.md`](paper/erdos_straus_hiperbola_english.md).

### Quick start – verification code

```bash
# Default (limit = 10,000,000)
python src/verify.py

# Custom limits
python src/verify.py --limit 1000000
python src/verify.py --limit 10000000
python src/verify.py --limit 50000000
python src/verify.py --limit 100000000
```

### Related resources

- The Erdős–Straus conjecture is listed as **problem 242** in the database of Erdős problems maintained by Terence Tao and collaborators:  
  [erdosproblems.com](https://www.erdosproblems.com) · [GitHub repository](https://github.com/teorth/erdosproblems)

- Related work on counting the number of solutions:  
  C. Elsholtz & T. Tao, *Counting the number of solutions to the Erdős–Straus equation on unit fractions*, J. Aust. Math. Soc. 94 (2013), 50–105.
  
## License

- **Code** (`src/` and scripts): [MIT License](LICENSE)
- **Paper and documentation** (Markdown): [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)
