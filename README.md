# special

[![CI](https://github.com/nabi-lang/special/actions/workflows/ci.yml/badge.svg)](https://github.com/nabi-lang/special/actions/workflows/ci.yml) [![packages.nabi-lang.org](https://img.shields.io/github/v/tag/nabi-lang/special?label=packages.nabi-lang.org&sort=semver)](https://packages.nabi-lang.org/packages/special) [![License: MIT OR Apache-2.0](https://img.shields.io/badge/license-MIT%20OR%20Apache--2.0-blue)](#license)

Special functions for Nabi: gamma and beta with their incomplete forms,
the error function family and the Faddeeva function, Bessel, Airy and
Struve functions, Riemann and Hurwitz zeta, polygamma, elliptic
integrals and Jacobi elliptic functions, exponential, sine, cosine and
Fresnel integrals, Lambert W, Legendre functions and spherical
harmonics, the classical orthogonal polynomials with their Gauss rules,
exact combinatorics, and the logistic / log-sum-exp helpers. Pure Nabi,
so it runs on every target, wasm included.

## Add it

```sh
nabi add special
```

Needs Nabi 0.1 or later.

## Usage

The functions take and return `f64` and follow SciPy's names and
conventions. An argument outside the domain gives NaN, as in libm; only
the calls that build tables (`roots_*`, `softmax`) throw a
`SpecialError` that says what to pass.

```nabi
use special::Elementary;
use core::array::{Array, Vector};

println(f"{special::gamma(4.5)} {special::lgamma(1e10)} {special::digamma(1.0)}");
println(f"{special::gammainc(2.0, 1.5)} {special::betaincinv(2.0, 5.0, 0.3)}");
println(f"{special::erfc(10.0)} {special::erfcx(30.0)} {special::ndtri(1e-10)}");
println(f"{special::jv(2.5, 10.0)} {special::kv(0.3, 2.0)} {special::airy(-3.0):?}");
println(f"{special::zeta(3.0)} {special::polygamma(2, 0.5)} {special::lambertw(-0.2, -1)}");
println(f"{special::ellipk(0.9)} {special::ellipj(0.7, 0.4):?} {special::e1(2.0)}");

// Any function maps over an array with the broadcast call; `apply`
// does the same on all cores for large arrays.
let x: Vector<f64> = [0.5, 1.0, 1.5];
let y = special::erf.(x);
let j = special::jv.(1.5, x);
let big = special::apply(special::gamma, Array::full([1000000], 2.5));

// Complex arguments.
let z = 1.0 + 2i;
println(f"{special::cgamma(z)} {special::cerf(z)} {special::wofz(z)} {special::czeta(0.5 + 14.134725i)}");
// The elementary functions core's `Complex` leaves out, as methods.
println(f"{z.sin()} {z.atanh()} {z.cbrt()} {(1e-10 + 1e-10i).expm1()}");

// Gauss-Legendre: integrate x^6 on [-1, 1] exactly with 4 nodes.
let (t, w) = try special::roots_legendre(4);
let s = (w .* t .** 6.0).sum();
assert((s - 2.0 / 7.0).abs() < 1e-15);

// Exact integers.
assert(special::comb_exact(100, 50) == 100891344545564193334812497256);
println(f"{special::factorial_exact(50)}");
```

## Notation

Greek letters, subscript digits, superscripts and primes are
identifier characters, so the textbook names are plain functions in
`special::notation`, each the same as the ASCII one:

| Name | Same as | Name | Same as |
|------|---------|------|---------|
| `Γ(x)`, `lnΓ(x)` | `gamma`, `lgamma` | `J₀ J₁ Y₀ Y₁` | `j0 j1 y0 y1` |
| `ψ(x)`, `ψ′(x)` | `digamma`, `trigamma` | `I₀ I₁ K₀ K₁` | `i0 i1 k0 k1` |
| `Β(a, b)` | `beta` | `Ai Bi Ai′ Bi′` | `ai bi aip bip` |
| `ζ(s)` | `zeta` | `E₁ Ei Si Ci` | `e1 ei si ci` |
| `Φ(x)`, `Φ⁻¹(p)` | `ndtr`, `ndtri` | `W₀(x)`, `W₋₁(x)` | `lambertw(x)`, `lambertw(x, -1)` |
| `erf⁻¹(y)` | `erfinv` | | |

```nabi
use special::notation::{Γ, ψ, ζ, Φ⁻¹, J₀};

assert(Γ(5.0) == 24.0);
assert((ψ(1.0) + 0.5772156649015329).abs() < 1e-16);
println(f"{ζ(2.0)} {Φ⁻¹(0.975)} {J₀(2.404825557695773)}");
```

There are no new operators: the factorial `n!` cannot be registered
(`!` is a fixed token with no postfix form, and `‼` is an emoji, so an
identifier character), and the other notations of the field are
function names. `2i` (core's imaginary literal) writes complex
arguments.

## What is in it

| Area | Items |
|------|-------|
| Gamma | `gamma`, `lgamma`, `lgamma1p`, `rgamma`, `digamma`, `trigamma`, `polygamma`, `gammainc`, `gammaincc`, `gammaincinv`, `gammainccinv`, `gammainc_pq`, `gammainc_factor`, `poch`, `sinpi`, `cospi`, `log1pmx` |
| Beta | `beta`, `lbeta`, `betainc`, `betaincc`, `betaincinv`, `betainccinv`, `betainc_pq`, `betainc_factor` |
| Error function | `erf`, `erfc`, `erfinv`, `erfcinv`, `erfcx`, `erfi`, `dawson`, `ndtr`, `ndtri`, `ndtri_pq`, `wofz` |
| Complex | `Elementary` (`sin`, `cos`, `tan`, `sinh`, `cosh`, `tanh`, their inverses, `log10`, `log2`, `cbrt`, `expm1`, `ln1p`, `is_close` as methods, C99 Annex G branch cuts), `cgamma`, `clgamma`, `crgamma`, `cdigamma`, `cerf`, `cerfc`, `cerfcx`, `cerfi`, `cdawson`, `czeta`, `wofz`, `hankel1`, `hankel2` |
| Bessel | `j0`, `j1`, `jn`, `jv`, `jve`, `y0`, `y1`, `yn`, `yv`, `yve`, `i0`, `i1`, `iv`, `i0e`, `i1e`, `ive`, `k0`, `k1`, `kn`, `kv`, `k0e`, `k1e`, `kve`, `hankel1`, `hankel2`, `hankel1e`, `hankel2e`, `spherical_jn`, `spherical_yn`, `spherical_in`, `spherical_kn` |
| Airy, Struve | `airy`, `airye`, `ai`, `aip`, `bi`, `bip`, `struve_h`, `struve_l` |
| Zeta | `zeta`, `zetac`, `hurwitz`, `czeta` |
| Elliptic | `ellipk`, `ellipkm1`, `ellipe`, `ellipkinc`, `ellipeinc`, `ellippi`, `ellippiinc`, `elliprf`, `elliprd`, `elliprj`, `elliprc`, `elliprg`, `ellipj` |
| Integrals | `e1`, `ei`, `expn`, `sici`, `si`, `ci`, `shichi`, `shi`, `chi`, `fresnel` |
| Lambert W | `lambertw` (branches 0 and -1) |
| Legendre | `assoc_legendre_p`, `lpmv`, `sph_legendre_p`, `sph_harm` |
| Polynomials | `eval_legendre`, `eval_chebyt`, `eval_chebyu`, `eval_hermite`, `eval_hermitenorm`, `eval_laguerre`, `eval_genlaguerre`, `eval_gegenbauer`, `eval_jacobi` |
| Gauss rules | `roots_legendre`, `roots_chebyt`, `roots_chebyu`, `roots_hermite`, `roots_hermitenorm`, `roots_laguerre`, `roots_genlaguerre`, `roots_gegenbauer`, `roots_jacobi` |
| Combinatorics | `factorial`, `factorial2`, `lfactorial`, `comb`, `multicomb`, `perm`, `binom`, `stirling2`, and the exact `int` forms `factorial_exact`, `factorial2_exact`, `comb_exact`, `perm_exact` |
| Logistic | `expit`, `logit`, `log_expit`, `log1pexp`, `log1mexp`, `logaddexp`, `logsumexp`, `logsumexp_axis`, `softmax`, `log_softmax` |
| Arrays | `apply` (parallel element-wise map) |

Methods, each cited in its function's docs: Taylor series about 2 and
Stirling's series for ln Gamma; DiDonato and Morris for the incomplete
gamma and beta functions; Chebyshev series of erfcx and Wichura's AS 241;
Cephes-layout rational and Chebyshev fits (refitted to 60-digit values)
for the integer-order Bessel functions; for real order Hankel's
expansion, Temme's series, the continued fractions of Barnett et al.
with the Wronskians and the trapezoidal rule on the integral of K;
Euler-Maclaurin and the functional equation for zeta; Zaghloul and
Ali's Algorithm 916 and Laplace's continued fraction for the Faddeeva
function; Hare's algorithm for the complex log Gamma; Carlson's
duplication for the symmetric elliptic integrals, the AGM and
descending Landen transformation for K, E and the Jacobi functions;
Golub-Welsch with Newton polishing for the Gauss rules. Each function's
docs state its accuracy: most are within a few ulp, measured against
mpmath at 40 or more digits.

Not covered: Bessel functions of complex argument, complex elliptic
integrals, complex Lambert W, Legendre functions of the second kind.

## Docs

https://docs.nabi-lang.org/packages/special/latest

## License

Licensed under either of [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE), at
your option. Parts derived from the Faddeeva package (MIT) and Cephes
(BSD-3-Clause) carry their notices in [NOTICE](NOTICE).
