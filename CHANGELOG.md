# Changelog

All notable changes to this package. Versions follow semver, independent of the Nabi toolchain.

## 0.1.0 (unreleased)

First release.

- Gamma and beta with incomplete forms and inverses, polygamma, `poch`.
- Error function family (`erf`, `erfc`, `erfinv`, `erfcx`, `ndtr`, `ndtri`)
  and the Faddeeva function `wofz`.
- Bessel (integer and real order, scaled, spherical, Hankel), Airy and
  Struve functions.
- Riemann and Hurwitz zeta, elliptic integrals (Legendre and Carlson forms)
  and Jacobi elliptic functions.
- Exponential, sine, cosine and Fresnel integrals, Lambert W (branches 0
  and -1), Legendre functions and spherical harmonics.
- Orthogonal polynomials with their Gauss rules, exact combinatorics,
  logistic and log-sum-exp helpers; complex forms of the main functions.
- `special::notation` (`Γ`, `ψ`, `ζ`, `Φ`, ...) and `apply` for arrays.
- f64, SciPy's names and conventions. Pure Nabi, every target including
  wasm. Needs Nabi 0.1 or later.
