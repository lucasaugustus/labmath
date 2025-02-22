labmath version 2.2.0
=====================

This repository is deprecated in favor of labmath3, which is available via GitHub (https://github.com/lucasaugustus/labmath3/) and PyPI (https://pypi.org/project/labmath/).

This is a module for basic math in the general vicinity of computational number theory.  It includes functions associated with primality testing, integer factoring, prime counting, linear recurrences, modular square roots, generalized Pell equations, the classic arithmetical functions, continued fractions, partitions, Størmer's theorem, smooth numbers, and Dirichlet convolution.  Integer arithmetic is used wherever feasible.

Functions
=========

We make a few imports:

.. code:: python

    from multiprocessing import Process, Queue as mpQueue
    from itertools import chain, count, groupby, islice, tee, cycle, takewhile, compress, product, zip_longest
    from fractions import Fraction
    from random import randrange
    from math import log, log2, ceil, sqrt, factorial; inf = float('inf')
    from heapq import merge
    
    try: from gmpy2 import mpz; mpzv, inttypes = 2, (int, type(mpz(1)))
    except ImportError: mpz, mpzv, inttypes = int, 0, (int,)
    
    labmathversion = "2.2.0"

The *new* functions provided by this module are as follows.  Further details, including examples and input details, are available in docstrings and accessible via the built-in ``help`` function.

.. code:: python

    primegen(limit=inf)

Generates primes less than the given limit (which may be infinite) lazily via a segmented sieve of Eratosthenes.  Memory usage depends on the sequence of prime gaps; on Cramér's conjecture, it is O(sqrt(*p*) · log(*p*)\ :sup:`2`).

.. code:: python

    rpn(instr)

Evaluates a string in reverse Polish notation.  The acceptable binary operators are ``+ - * / // % **`` and correspond directly to those same operators in Python3 source code.  The acceptable unary operators are ``! #``.  They are the factorial and primorial, respectively.  There are four aliases: ``x`` for ``*`` , ``xx`` for ``**`` , ``f`` for ``!``, and ``p!`` for ``#``.

.. code:: python

    iterprod(l)

Product of the elements of any iterable.  The product of an empty iterable is 1.  DEPRECATED: now that Python 3.8 has ``math.prod``, this function definition will in a future version be replaced with the statement ``from math import prod``.  That will probably happen when PyPy3 supports Python 3.8.

.. code:: python

    listprod(l)

Product of the elements of a list.  The product of the empty list is 1.  We use a binary algorithm because this can easily generate huge numbers, and calling ``reduce(lambda x,y: x*y, a)`` in such situations is quite a bit slower due to the time-complexity of multiplication.  However, the size of the problem required to make this superior to ``iterprod()`` is quite large, so ``iterprod()`` should usually be used instead.

.. code:: python

    polyval(f, x, m=None)

Evaluates a polynomial at a particular point, optionally modulo something.

.. code:: python

    binomial(n,k)

The binomial coefficient nCr(``n``, ``k``).  DEPRECATED: now that Python 3.8 has ``math.comb``, this function definition will in a future version be replaced with the statement ``from math import comb``.  That will probably happen when PyPy3 supports Python 3.8.

.. code:: python

    powerset(l)

Generates the powerset of a list, tuple, or string.  The yielded objects are always lists.

.. code:: python

    primephi(x, a, ps, phicache={})

Legendre's phi function.  Helper function for ``primepi``.

.. code:: python

    primepi(x, ps=[], picache={}, phicache={}, sqrts={})

Computes the number of primes ≤ ``x`` via the Meissel-Lehmer method.  The arguments ``ps``, ``pichache``, ``phicache``, and ``sqrts`` are for internal use only.

.. code:: python

    primesum(n)

Sum of primes ≤ ``n``.

.. code:: python

    altseriesaccel(a, n)

Convergence acceleration for alternating series.  This is algorithm 1 from *Convergence Acceleration of Alternating Series* by Cohen, Villegas, and Zagier `(pdf)`__, with a minor tweak so that the *d*-value is not computed via floating point.

__ https://people.mpim-bonn.mpg.de/zagier/files/exp-math-9/fulltext.pdf

.. code:: python

    riemannzeta(n, k=24)

Computes the Riemann zeta function by applying ``altseriesaccel`` to the `Dirichlet eta function`__.  Should be rather accurate throughout the complex plane except near ``n`` such that 1 = 2\ :sup:`n-1`.

__ https://en.wikipedia.org/wiki/Dirichlet_eta_function

.. code:: python

    zetam1(n, k=24)

Computes ``riemannzeta(n, k) - 1`` by applying ``altseriesaccel`` to the Dirichlet eta function.  Designed to be accurate even when ``riemannzeta(n)`` is machine-indistinguishable from 1.0 --- in particular, when ``n`` is a large real number.

.. code:: python

    riemannR(x, n=None, zc={})

Uses the `Gram series`__ to compute `Riemann's R function`__, which is a rather good approximation to ``primepi``.  The argument ``zc`` is a cache of zeta values.

__ http://mathworld.wolfram.com/GramSeries.html
__ http://mathworld.wolfram.com/RiemannPrimeCountingFunction.html

.. code:: python

    nthprimeapprox(n)

Produces an integer that should be rather close to the ``n``\ :sup:`th` prime by using binary splitting on Riemann's R function.

.. code:: python

    nthprime(n)

Returns the ``n``\ :sup:`th` prime (counting 2 as #1).  This is done with some efficiency by using ``nthprimeapprox`` as an initial estimate, computing ``primepi`` of that, and then sieving to remove the error.

.. code:: python

    gcd(a, *r)

Greatest common divisor of any number of values.  Now that ``math.gcd`` supports any number of arguments, this function definition will in some future version be replaced with ``from math import gcd``.

.. code:: python

    xgcd(a, b)

Extended Euclidean altorithm: returns a tuple (**g**, *x*, *y*) such that *g* = gcd(``a``, ``b``) and *g* = ``a``·*x* + ``b``·*y*.

.. code:: python

    modinv(a, m)

Returns the inverse of ``a`` modulo ``m``, normalized to lie between ``0`` and ``m-1``.  If ``a`` is not coprime to ``m``, returns 1.  DEPRECATED: as of version 3.8, this can be computed using Python's built-in pow function as ``pow(a, -1, m)``.  As such, a future version of this library will remove this function.  That will probably happen once PyPy3 supports Python 3.8.

.. code:: python

    crt(rems, mods)

Returns the unique integer *c* in ``range(iterprod(mods))`` such that *c* ≡ *x* (mod *y*) for (*x*, *y*) in ``zip(rems, mods)``.  All elements of ``mods`` must be pairwise coprime.

.. code:: python

    lcm(a, *r)

The least common multiple of any number of values.  Now that ``math.lcm`` supports any number of arguments, a future version of this library will replace this function definition with ``from math import lcm``.

.. code:: python

    isqrt(n)

Greatest integer whose square is ≤ ``n``.  Now that Python 3.9 has the ``math.isqrt`` function, a future version of this library will remove this function definition in favor of the line ``from math import isqrt``.

.. code:: python

    introot(n, r=2)

For non-negative ``n``, returns the greatest integer ≤ the ``r``\ :sup:`th` root of ``n``.  For negative ``n``, returns the least integer ≥ the ``r``\ :sup:`th` root of ``n``, or ``None`` if ``r`` is even.

.. code:: python

    semiprimegen()

Generates the semiprimes.  This is done by filtering the primes out of the output of ``pspgen``.

.. code:: python

    pspgen()

Generates the primes and semiprimes.  This is done using a segmented sieve based on the sieve of Eratosthenes and the fact that these are precisely the numbers not divisible by any smaller semiprimes.

.. code:: python

    almostprimegen(k)

Generates the ``k``-almost-primes, which are the numbers that have precisely ``k`` prime factors, counted with multiplicity.  This is done by filtering ``nearlyprimegen(k-1)`` out of the output of ``nearlyprimegen(k)``.

.. code:: python

    nearlyprimegen(k)

Generates the numbers (other than 1) that have ``k`` or fewer prime factors, counted with multipicity.  This is done via a segmented sieve based on the sieve of Eratosthenes and the fact that these are precisely the numbers not divisible by any smaller ``k``-almost-primes.

.. code:: python

    ispower(n, r=0)

If ``r`` = 0: If ``n`` is a perfect power, return a tuple containing the largest integer that, when squares/cubed/etc, yields ``n`` as the first component and the relevant power as the second component.  If ``n`` is not a perfect power, return ``None``.

If ``r`` > 0: We check whether ``n`` is a perfect ``r``\ :sup:`th` power; we return its ``r``\ :sup:`th` root if it is and ``None`` if it is not.

.. code:: python

    ilog(x, b)

Greatest integer *k* such that ``b``\ :sup:`k` ≤ ``x``.

.. code:: python

    fibogen()

Generates the Fibonacci numbers, starting with 0 and 1.

.. code:: python

    fibo(n, f={0:0, 1:1, 2:1})

Efficiently extracts the ``n``\ :sup:`th` Fibonacci number, indexed so that ``fibo(0)`` = 0 and ``fibo(1)`` = ``fibo(2)`` = 1.  The argument ``f`` is used for memoization.  We compute O(log(``n``)) earlier Fibonaccis along the way.  This is, in the big-O sense, just about as fast as possible.

.. code:: python

    fibomod(n, m, f={0:0, 1:1, 2:1})

Efficiently extracts the nth Fibonacci number modulo ``m``, indexed so that ``fibo(0)`` = 0 and ``fibo(1)`` == ``fibo(2)`` = 1.  The argument ``f`` is used for memoization.  We compute O(log(``n``)) earlier Fibonaccis along the way.  This is the asymptotically fastest algorithm.

.. code:: python

    lucaschain(n, x0, x1, op1, op2)

Algorithm 3.6.7 from *Prime Numbers: A Computational Perspective* by Crandall & Pomerance (2\ :sup:`nd` edition): Evaluation of a binary Lucas chain.  To quote their description:

    For a sequence *x*\ :sub:`0`, *x*\ :sub:`1`, ... with a rule for computing *x*\ :sub:`2j` from *x*\ :sub:`j` and a rule for computing *x*\ :sub:`2j+1` from *x*\ :sub:`j` and *x*\ :sub:`j+1`, this algorithm computes (*x*\ :sub:`n`, *x*\ :sub:`n+1`) for a given positive integer *n*.  We have *n* in binary as (*n*\ :sub:`0`, *n*\ :sub:`1`, ..., *n*\ :sub:`b-1`) with *n*\ :sub:`0` being the low-order bit.  We write the rules as follows: *x*\ :sub:`2j` = op1(*x*\ :sub:`j`) and *x*\ :sub:`2j+1` = op2(*x*\ :sub:`j`, *x*\ :sub:`j+1`).

.. code:: python

    lucasgen(P, Q):

Generates the Lucas U- and V-sequences with parameters (``P``, ``Q``).

.. code:: python

    lucas(k, P, Q)

Efficiently computes the ``k``\ :sup:`th` terms in the Lucas U- and V-sequences U(``P``, ``Q``) and V(``P``, ``Q``).  More explicitly, if

    U\ :sub:`0`, U\ :sub:`1`, V\ :sub:`0`, V\ :sub:`1` = 0, 1, 2, ``P``

and we have the recursions

    U\ :sub:`n` = ``P`` · U\ :sub:`n-1` - ``Q`` · U\ :sub:`n-2`

    V\ :sub:`n` = ``P`` · V\ :sub:`n-1` - ``Q`` · V\ :sub:`n-2`

then we compute U\ :sub:`k` and V\ :sub:`k` in O(ln(``k``)) arithmetic operations.  If ``P``\ :sup:`2` ≠ 4·``Q``, then these sequences grow exponentially, so the number of bit operations is anywhere from O(``k`` · ln(``k``)\ :sup:`2` · ln(ln(``k``))) to O(``k``\ :sup:`2`) depending on how multiplication is handled.  We recommend using MPZs when ``k`` > 100 or so.  We divide by ``P``\ :sup:`2` - 4·``Q`` at the end, so we handle separately the case where this is zero.

.. code:: python

    binlinrecgen(P, Q, a, b)

The general binary linear recursion.  Exactly like ``lucasgen``, except we only compute one sequence, and we supply the seeds.

.. code:: python

    binlinrec(k, P, Q, a, b)

The general binary linear recursion.  Exactly like ``lucas``, except we compute only one sequence, and we supply the seeds.

.. code:: python

    linrecgen(a, b, m=None)

The general homogenous linear recursion: we generate in order the sequence defined by

    *x*\ :sub:`n+1` = ``a``\ :sub:`k` · *x*\ :sub:`n` + ``a``\ :sub:`k-1` · *x*\ :sub:`n-1` + ... + ``a``\ :sub:`0` · *x*\ :sub:`n-k`,

where the initial values are [*x*\ :sub:`0`, ..., *x*\ :sub:`k`] = ``b``.  If ``m`` is supplied, then we compute the sequence modulo ``m``.  The terms of this sequence usually grow exponentially, so computing a distant term incrementally by plucking it out of this generator takes O(``n``\ :sup:`2`) bit operations.  Extractions of distant terms should therefore be done via ``linrec``, which takes anywhere from O(``n`` · ln(``n``)\ :sup:`2` · ln(ln(``n``))) to O(``n``\ :sup:`2`) bit operations depending on how multiplication is handled.

.. code:: python

    linrec(n, a, b, m=None)

The general homogeneous linear recursion.  If ``m`` is supplied, terms are computed modulo ``m``.  We use matrix methods to efficiently compute the ``n``\ :sup:`th` term of the recursion

    *x*\ :sub:`n+1` = ``a``\ :sub:`k` · *x*\ :sub:`n` + ``a``\ :sub:`k-1` · *x*\ :sub:`n-1` + ... + ``a``\ :sub:`0` · *x*\ :sub:`n-k`,

where the initial values are [*x*\ :sub:`0`, ..., *x*\ :sub:`k`] = ``b``.

.. code:: python

    legendre(a, p)

Legendre symbol (``a`` | ``p``): 1 if ``a`` is a quadratic residue mod ``p``, -1 if it is not, and 0 if ``a`` ≡ 0 (mod ``p``).  Not meaningful if ``p`` is not prime.

.. code:: python

    jacobi(a, n)

The Jacobi symbol (``a`` | ``n``).

.. code:: python

    kronecker(a, n)

The Kronecker symbol (``a`` | ``n``).  Note that this is the generalization of the Jacobi symbol, *not* the Dirac-delta analogue.

.. code:: python

    sprp(n, b)

The strong probable primality test (aka single-round Miller-Rabin).

.. code:: python

    mrab(n, basis)

Miller-Rabin probable primality test.

.. code:: python

    miller(n)

Miller's primality test.  If the extended Riemann hypothesis (the one about Dirichlet L-functions) is true, then this test is deterministic.

.. code:: python

    lprp(n, a, b)

Lucas probable primality test as described in *Prime Numbers: A Computational Perspective* by Crandall & Pomerance (2\ :sup:`nd` edition).

.. code:: python

    lucasmod(k, P, Q, m)

Efficiently computes the ``k``\ :sup:`th` terms of Lucas U- and V-sequences modulo ``m`` with parameters (``P``, ``Q``).  Currently just a helper function for ``slprp`` and ``xslprp``.  Will be upgraded to full status when the case ``gcd(D,m)!=1`` is handled properly.

.. code:: python

    slprp(n, a, b)

Strong lucas probable primality test as described on Wikipedia.  Its false positives are a strict subset of those for ``lprp`` with the same parameters.

.. code:: python

    xslprp(n, a)

Extra strong Lucas probable primality test as described on Wikipedia.  Its false positives are a strict subset of those for ``slprp`` (and therefore ``lprp``) with parameters (``a``, 1).

.. code:: python

    bpsw(n)

The Baille-Pomerance-Selfridge-Wagstaff probable primality test.  Infinitely many false positives are conjectured to exist, but none are known, and the test is known to be deterministic below 2\ :sup:`64`.

.. code:: python

    qfprp(n, a, b)

Quadratic Frobenius probable primality test as described in *Prime Numbers: A Computational Perspective* by Crandall & Pomerance (2\ :sup:`nd` edition).

.. code:: python

    polyaddmodp(a, b, p)

Adds two polynomials and reduces their coefficients mod ``p``.  Polynomials are written as lists of integers with the constant terms first.  If the high-degree coefficients are zero, those terms will be deleted from the answer so that the highest-degree term is nonzero.  We assume that the inputs also satisfy this property.  The zero polynomial is represented by the empty list.  If one of the input polynomials is ``None``, we return ``None``.

.. code:: python

    polysubmodp(a, b, p)

Subtracts the polynomial ``b`` from ``a`` and reduces their coefficients mod ``p``.  Polynomials are written as lists of integers with the constant terms first.  If the high-degree coefficients are zero, those terms will be deleted from the answer so that the highest-degree term is nonzero.  We assume that the inputs also satisfy this property.  The zero polynomial is represented by the empty list.  If one of the input polynomials is ``None``, we return ``None``.

.. code:: python

    polymulmodp(a, b, p)

Multiplies the polynomials ``a`` and ``b`` and reduces their coefficients mod ``p``.  Polynomials are written as lists of integers with the constant terms first.  If the high-degree coefficients are zero, those terms will be deleted from the answer so that the highest-degree term is nonzero.  We assume that the inputs also satisfy this property.  The zero polynomial is represented by the empty list.  If one of the input polynomials is ``None``, we return ``None``.

.. code:: python

    polydivmodmodp(a, b, p)

Divides the polynomial ``a`` by the polynomial ``b`` and returns the quotient and remainder.  The coefficients are interpreted mod ``p``.  Polynomials are written as lists of integers with the constant terms first.  If the high-degree coefficients are zero, those terms will be deleted from the answer so that the highest-degree term is nonzero.  We assume that the inputs also satisfy this property.  The zero polynomial is represented by the empty list.  If one of the input polynomials is ``None``, we return ``None``.  The result is not guaranteed to exist; in such cases we return ``(None, None)``.

.. code:: python

    gcmd(f, g, p)

Computes the greatest common monic divisor of the polynomials ``f`` and ``g``.  The coefficients are interpreted mod ``p``.  Polynomials are written as lists of integers with the constant terms first.  If the high-degree coefficients are zero, those terms will be deleted from the answer so that the highest-degree term is nonzero.  We assume that the inputs also satisfy this property.  The zero polynomial is represented by the empty list.  If one of the input polynomials is ``None``, or if both input polynomials are ``[]``, we return ``None``.  The result is not guaranteed to exist; in such cases, we return ``None``.  Coded after algorithm 2.2.1 from *Prime Numbers: A Computational Perspective* by Crandall & Pomerance (2\ :sup:`nd` edition).

.. code:: python

    polypowmodpmodpoly(a, e, p, f)

Computes the remainder when the polynomial ``a`` is raised to the ``e``\ :sup:`th` power and reduced modulo ``f``.  The coefficients are interpreted mod ``p``.  Polynomials are written as lists of integers with the constant terms first.  If the high-degree coefficients are zero, those terms will be deleted from the answer so that the highest-degree term is nonzero.  We assume that the inputs also satisfy this property.  The zero polynomial is represented by the empty list.  If one of the input polynomials is ``None``, or if ``f == []``, we return ``None``.  The answer is not guaranteed to exist.  In such cases, we return ``None``.

.. code:: python

    frobenius_prp(n, poly, strong=False)

Grantham's general Frobenius probable primality test, in both the strong and weak versions, as described in `his paper introducing the test`__.

__ https://doi.org/10.1090/S0025-5718-00-01197-2

.. code:: python

    isprime(n, tb=(3,5,7,11,13,17,19,23,29,31,37,41,43,47,53,59))

The workhorse primality test.  It is a BPSW primality test variant: we use the strong Lucas PRP test and preface the computation with trial division for speed.  No composites are known to pass the test, though it is suspected that infinitely many will do so.  There are definitely no such errors below 2\ :sup:`64`.  This function is mainly a streamlined version of ``bpsw``.

.. code:: python

    isprimepower(n)

Determines whether ``n`` is of the form *p*\ :sup:`e` for some prime number *p* and positive integer *e*, and returns (*p*, *e*) if so.

.. code:: python

    isprime_mersenne(p)

The Lucas-Lehmer test.  Deterministically and efficiently checks whether the Mersenne number 2\ :sup:`p`-1 is prime.

.. code:: python

    nextprime(n, primetest=isprime)

Smallest prime strictly greater than ``n``.

.. code:: python

    prevprime(n, primetest=isprime)

Largest prime strictly less than ``n``, or ``None`` if no such prime exists.

.. code:: python

    randprime(digits, base=10, primetest=isprime)

Returns a random prime with the specified number of digits when rendered in the specified base.

.. code:: python

    randomfactored(n, primetest=isprime)

Efficiently generates an integer selected uniformly from the range [1, ``n``] with its factorization.  Uses Adam Kalai's algorithm, which uses in the average case O(log(``n``)\ :sup:`2`) primality tests.  When called with the default primality test, this then uses O(log(``n``)\ :sup:`3`) arithmetic operations, which in turn results in just over O(log(``n``)\ :sup:`4`) to O(log(``n``)\ :sup:`5`) bit operations, depending on how multiplication is handled.

.. code:: python

    sqrtmod_prime(a, p)

Finds *x* such that *x*\ :sup:`2` ≡ ``a`` (mod ``p``).  We assume that ``p`` is a prime and ``a`` is a quadratic residue modulo ``p``.  If any of these conditions is false, then the return value is meaningless.

.. code:: python

    cbrtmod_prime(a, p)

Returns in a sorted list all cube roots of a mod p.  There are a bunch of easily-computed special formulae for various cases with ``p`` != 1 (mod 9); we do those first, and then if ``p`` == 1 (mod 9) we use Algorithm 4.2 in `Taking Cube Roots in Zm`__ by Padro and Saez, which is essentially a variation on the Tonelli-Shanks algorithm for modular square roots.

__ https://doi.org/10.1016/S0893-9659(02)00031-9

.. code:: python

    pollardrho_brent(n)

Factors integers using Brent's variation of Pollard's rho algorithm.  If ``n`` is prime, we immediately return ``n``; if not, we keep chugging until a nontrivial factor is found.

.. code:: python

    pollard_pm1(n, B1=100, B2=1000)

Integer factoring function.  Uses Pollard's p-1 algorithm.  Note that this is only efficient if the number to be factored has a prime factor *p* such that *p*-1's largest prime factor is "small".

.. code:: python

    mlucas(v, a, n)

Helper function for ``williams_pp1``.  Multiplies along a Lucas sequence modulo ``n``.

.. code:: python

    williams_pp1(n)

Integer factoring function.  Uses Williams' p+1 algorithm, single-stage variant.  Note that this is only efficient when the number to be factored has a prime factor *p* such that *p*\ +1's largest prime factor is "small".

.. code:: python

    ecadd(p1, p2, p0, n)

Helper function for ``ecm``.  Adds two points on a Montgomery curve modulo ``n``.

.. code:: python

    ecdub(p, A, n)

Helper function for ``ecm``.  Doubles a point on a Montgomery curve modulo ``n``.

.. code:: python

    ecmul(m, p, A, n)

Helper function for ``ecm``.  Multiplies a point on Montgomery curve by an integer modulo ``n``.

.. code:: python

    secm(n, B1, B2, seed)

Seeded elliptic curve factoring using the two-phase algorithm on Montgomery curves.  Helper function for ``ecm``.  Returns a possibly-trivial divisor of ``n`` given two bounds and a seed.

.. code:: python

    ecmparams(n)

Generator of parameters to use for ``secm``.

.. code:: python

    ecm(n, paramseq=ecmparams, nprocs=1)

Integer factoring via elliptic curves using the two-phase algorithm on Montgomery curves, and optionally uses multiple processes.  This is a shell function that repeatedly calls ``secm`` using parameters provided by ``ecmparams``; the actual factoring work is done there.  Multiprocessing incurs relatively significant overhead, so when ``nprocs==1`` (default), we don't call the multiprocessing functions.

.. code:: python

    siqs(n)

Factors an integer via the self-initializing quadratic sieve.  Most of this function is copied verbatim from https://github.com/skollmann/PyFactorise.

.. code:: python

    multifactor(n, methods)

Integer factoring function.  Uses several methods in parallel.  Waits for one of them to return, kills the rest, and reports.

.. code:: python

    primefac(n, trial=1000, rho=42000, primetest=isprime, methods=(pollardrho_brent,))

The workhorse integer factorizer.  Generates the prime factors of the input.  Factors that appear *x* times are yielded *x* times.

.. code:: python

    factorint(n, trial=1000, rho=42000, primetest=isprime, methods=(pollardrho_brent,))

Compiles the output of ``primefac`` into a dictionary with primes as keys and multiplicities as values.

.. code:: python

    factorsieve(stop)

Uses a sieve to compute the factorizations of all whole numbers strictly less than the input.  This uses a lot of memory; if you aren't after the factors directly, it's usually better to write a dedicated function for whatever it is that you actually want.

.. code:: python

    divisors(n)

Generates all natural numbers that evenly divide ``n``.  The output is not necessarily sorted.

.. code:: python

    divisors_factored(n)

Generates the divisors of ``n``, written as their prime factorizations in factorint format.

.. code:: python

    divcount(n)

Counts the number of divisors of ``n``.

.. code:: python

    divsigma(n, x=1)

Sum of divisors of a natural number, raised to the *x*\ :sup:`th` power.  The conventional notation for this in mathematical literature is σ\ :sub:`x`\ (``n``), hence the name of this function.

.. code:: python

    divcountsieve(stop)

Uses a sieve to compute the number of divisors of all whole numbers strictly less than the input.

.. code:: python

    totient(n, k=1)

Jordan's totient function: the number of ``k``-tuples of positive integers all ≤ ``n`` that form a coprime (``k``\ +1)-tuple together with ``n``.  When ``k`` = 1, this is Euler's totient: the number of numbers less than a number that are relatively prime to that number.

.. code:: python

    totientsieve(n)

Uses a sieve to compute the totients up to (and including) ``n``.

.. code:: python

    totientsum(n)

Computes ``sum(totient(n) for n in range(1, n+1))`` efficiently.

.. code:: python

    mobius(n)

The Möbius function of ``n``: 1 if ``n`` is squarefree with an even number of prime factors, -1 if ``n`` is squarefree with an odd number of prime factors, and 0 if ``n`` has a repeated prime factor.

.. code:: python

    mobiussieve(stop)

Uses a sieve to compute the Möbius function of all whole numbers strictly less than the input.

.. code:: python

    liouville(n)

The Liouville lambda function of ``n``: the strongly multiplicative function that is -1 on the primes.

.. code:: python

    polyroots_prime(g, p, sqfr=False)

Generates with some efficiency and without multiplicity the zeros of a polynomial modulo a prime.  Coded after algorithm 2.3.10 from *Prime Numbers: A Computational Perspective* by Crandall & Pomerance (2\ :sup:`nd` edition), which is essentially Cantor-Zassenhaus.

.. code:: python

    hensel(f, p, k, given=None)

Uses Hensel lifting to generate with some efficiency all zeros of a polynomial modulo a prime power.

.. code:: python

    sqrtmod(a, n)

Computes all square roots of ``a`` modulo ``n`` and returns them in a sorted list.

.. code:: python

    polyrootsmod(pol, n)

Computes the zeros of a polynomial modulo an integer.  We do this by factoring the modulus, solving modulo the prime power factors, and putting the results back together via the Chinese Remainder Theorem.

.. code:: python

    PQa(P, Q, D)

Generates some sequences related to simple continued fractions of certain quadratic surds.  A helper function for ``pell``.  Let ``P``, ``Q``, and ``D`` be integers such that ``Q`` ≠ 0, ``D`` > 0 is a nonsquare, and ``P``\ :sup:`2` ≡ ``D`` (mod ``Q``). We yield a sequence of tuples (*B*\ :sub:`i`, *G*\ :sub:`i`, *P*\ :sub:`i`, *Q*\ :sub:`i`) where *i* is an index counting up from 0, *x* = (``P``\ +√\ ``D``)/``Q`` = [*a*\ :sub:`0`; *a*\ :sub:`1`, *a*\ :sub:`2`, ...], (*P*\ :sub:`i`\ +√\ ``D``))/*Q*\ :sub:`i` is the *i*\ :sup:`th` complete quotient of *x*, and *B*\ :sub:`i` is the denominator of the *i*\ :sup:`th` convergent to *x*.  For full details, see https://web.archive.org/web/20180831180333/http://www.jpr2718.org/pell.pdf.

.. code:: python

    pell(D, N)

This function solves the generalized Pell equation: we find all non-negative integers (*x*, *y*) such that *x*\ :sup:`2` - ``D`` · *y*\ :sup:`2` = ``N``.  We have several cases:

Case 1: ``N`` = 0.  We solve *x*\ :sup:`2` = ``D`` · *y*\ :sup:`2`.  (0,0) is always a solution.

    Case 1a: If ``D`` is a nonsquare, then there are no further solutions.

    Case 1b: If ``D`` is a square, then there are infinitely many solutions, parametrized by (*t*·√\ ``D``, *t*).

Case 2: ``N`` ≠ 0 = ``D``.  We solve *x*\ :sup:`2` = ``N``.

    Case 2a: If ``N`` is a nonsquare, then there are no solutions.

    Case 2b: If ``N`` is a square, then there are infinitely many solutions, parametrized by (√\ ``N``, *t*).

Case 3: ``N`` ≠ 0 > ``D``.  We solve *x*\ :sup:`2` + \|\ ``D``\| · *y*\ :sup:`2` = ``N``.  The number of solutions will be finite.

Case 4: ``N`` ≠ 0 < ``D``.  We find lattice points on a hyperbola.

    Case 4a: If ``D`` is a square, then the number of solutions will be at most finite.  This case is solved by factoring.

    Case 4b: If ``D`` is a nonsquare, then we run the PQa/LMM algorithms: we produce a set of primitive solutions; if this set is empty, there are no solutions; if this set has members, an ininite set of solutions can be produced by repeatedly composing them with the fundamental solution of *x*\ :sup:`2` - ``D`` · *y*\ :sup:`2` = 1.

References:

* https://web.archive.org/web/20180831180333/https://www.jpr2718.org/pell.pdf
* http://www.offtonic.com/blog/?p=12
* http://www.offtonic.com/blog/?p=18

Input: ``D``, ``N`` -- integers

Output:

    A 3-tuple.

    If the number of solutions is finite, it is ``(None, z, None)``, where ``z`` is the sorted list of all solutions.

    If the number of solutions is infinite and the equation is degenerate, it's ``(gen, None, None)``, where ``gen`` yields all solutions.

    If the number of solutions if infinite and the equation is nondegenerate, it is ``(gen, z, f)``, where ``z`` is the set of primitive solutions, represented as a sorted list, and ``f`` is the fundamental solution --- i.e., ``f`` is the primitive solution of *x*\ :sup:`2` - ``D`` · *y*\ :sup:`2` = 1.

    Note that we can check the infinitude of solutions by calling ``bool(pell(D,N)[0])``.

.. code:: python

    simplepell(D, bail=inf)

Generates the positive solutions of *x*\ :sup:`2` - ``D`` · *y*\ :sup:`2` = 1.  We use some optimizations specific to this case of the Pell equation that makes this more efficient than calling ``pell(D,1)[0]``.  Note that this function is not equivalent to calling ``pell(D,1)[0]``: ``pell`` is concerned with the general equation, which may or may not have trivial solutions, and as such yields all non-negative solutions, whereas this function is concerned only with the simple Pell equation, which always has an infinite family of positive solutions generated from a single primitive solution and always has the trivial solution (1,0).

We yield only those solutions with *x* ≤ ``bail``.

.. code:: python

    carmichael(n)

The Carmichael lambda function: the smallest positive integer *m* such that *a*\ :sup:`m` ≡ 1 (mod ``n``) for all *a* such that gcd(*a*, ``n``) = 1.  Also called the reduced totient or least universal exponent.

.. code:: python

    multord(b, n)

Computes the multiplicative order of ``b`` modulo ``n``; i.e., finds the smallest *k* such that ``b``\ :sup:`k` ≡ 1 (mod ``n``).

.. code:: python

    pythags_by_perimeter(p)

Generates all Pythagorean triples of a given perimeter by examining the perimeter's factors.

.. code:: python

    collatz(n)

Generates the Collatz sequence initiated by ``n``.  Stops after yielding 1.

.. code:: python

    sqrtcfrac(n)

Computes the simple continued fraction for √\ ``n``.  We return the answer as ``(isqrt(n), [a,b,c,...,d])``, where ``[a,b,c,...,d]`` is the minimal reptend.

.. code:: python

    convergents(a)

Generates the convergents of a simple continued fraction.

.. code:: python

    contfrac_rat(n, d)

Returns the simple continued fraction of the rational number ``n``/``d``.

.. code:: python

    quadratic_scf(P,Q,D)

Computes the simple continued fraction of the expression (``P`` + sqrt(``D``)) / ``Q``, for any integers ``P``, ``Q``, and ``D`` with ``D`` ≥ 0 ≠ ``Q``.  Note that ``D`` can be a square or a nonsquare.

.. code:: python

    ngonal(x, n)

Returns the ``x``\ :sup:`th` ``n``-gonal number.  Indexing begins with 1 so that ``ngonal(1, n)`` = 1 for all applicable ``n``.

.. code:: python

    is_ngonal(p, n)

Checks whether ``p`` is an ``n``-gonal number.

.. code:: python

    partitions(n, parts=[1])

Computes with some semblance of efficiency the number of additive partitions of an integer.  The ``parts`` argument is for memoization.

.. code:: python

    partgen(n)

Generates partitions of integers in ascending order via an iterative algorithm.  It is the fastest known algorithm as of June 2014.

.. code:: python

    partconj(p)

Computes the conjugate of a partition.

.. code:: python

    farey(n)

Generates the Farey sequence of maximum denominator ``n``.  Includes 0/1 and 1/1.

.. code:: python

    fareyneighbors(n, p, q)

Returns the neighbors of ``p``/``q``  in the Farey sequence of maximum denominator ``n``.

.. code:: python

    ispractical(n)

Tests whether ``n`` is a practical number -- i.e., whether every integer from 1 through ``n`` (inclusive) can be written as a sum of divisors of ``n``.  These are also called panarithmic numbers.

.. code:: python

    hamming(ps, *ps2)

Generates all ``ps``-smooth numbers, where ``ps`` is a list of primes.

.. code:: python

    arithmeticderivative(n)

The arithmetic derivative of ``n``: if ``n`` is prime, then ``n``' = 1; if -2 < ``n`` < 2, then ``n``' = 0; if ``n`` < 0, then ``n``' = -(-``n``)'; and (*ab*)' = *a*'·*b* + *b*'·*a*.

.. code:: python

    perfectpowers()

Generates the sequence of perfect powers without multiplicity.

.. code:: python

    sqfrgen(ps)

Generates the squarefree products of the elements of ``ps``.

.. code:: python

    sqfrgenb(ps, b, k=0, m=1)

Generates the squarefree products of elements of ``ps``.  Does not yield anything > ``b``.  For best performance, ``ps`` should be sorted in decreasing order.

.. code:: python

    stormer(ps, *ps2, abc=None)

Størmer's theorem asserts that for any given set ``ps`` of prime numbers, there are only finitely many pairs of consecutive integers that are both ``ps``-smooth; the theorem also gives an effective algorithm for finding them.  We implement Lenstra's improvement to this theorem.

The ``abc`` argument indicates that we are to assume an effective abc conjecture of the form *c* < ``abc[0]`` · rad(*a*·*b*·*c*)\ :sup:`abc[1]`.  This enables major speedups.  If ``abc`` is ``None``, then we make no such assumptions.

.. code:: python

    quadintroots(a, b, c)

Given integers ``a``, ``b``, and ``c``, we return in a tuple all distinct integers *x* such that ``a``·*x*\ :sup:`2` + ``b``·*x* + ``c`` = 0.  This is primarily a helper function for ``cubicintrootsgiven`` and ``cubicintroots``.

.. code:: python

    cubicintrootsgiven(a, b, c, d, r)

Given integers ``a``, ``b``, ``c``, ``d``, and ``r`` such that ``a``·``r``\ :sup:`3` + ``b``·``r``\ :sup:`2` + ``c``·``r`` + ``d`` = 0, we find the cubic's other two roots and return in a tuple all distinct integer roots (including ``r``).  This is primarily a helper function for ``cubicintroots``.

.. code:: python

    cubicintroots(a, b, c, d)

Given integers ``a``, ``b``, ``c``, ``d``, we return in a tuple all distinct integer roots of ``a``·*x*\ :sup:`3` + ``b``·*x*\ :sup:`2` + ``c``·*x* + ``d``.  This is primarily a helper function for ``isprime_nm1``.

.. code:: python

    isprime_nm1(n, fac=None)

The *n*-1 primality test: given an odd integer ``n`` > 214 and a fully-factored integer *F* such that *F* divides ``n``-1 and *F* > ``n``\ :sup:`0.3`, we quickly determine without error whether ``n`` is prime.  If the provided (partial) factorization of ``n``-1 is insufficient, we compute the factorization ourselves.

.. code:: python

    isprime_np1(n, fac=None)

The *n*\ +1 primality test: given an odd integer ``n`` > 214 and a fully-factored integer *F* such that *F* divides ``n``\ +1 and *F* > ``n``\ :sup:`0.3`, we quickly determine without error whether ``n`` is prime.  If the provided (partial) factorization of ``n``\ +1 is insufficient, we compute the factorization ourselves.

.. code:: python

    mulparts(n, r=None, nfac=None)

Generates all ordered ``r``-tuples of positive integers whose product is ``n``.  If ``r`` is ``None``, then we generate all such tuples (regardless of size) that do not contain 1.

.. code:: python

    dirconv(f, g, ffac=False, gfac=False)

This returns a function that is the Dirichlet convolution of ``f`` and ``g``.  When called with the keyword arguments at their default values, this is equivalent to the expression ``lambda n: sum(f(d) * g(n//d) for d in divisors(n))``.  If ``f`` or ``g`` needs to factor its argument, such as ``f == totient`` or ``g == mobius`` or something like that, then that lambda expression calls the factorizer a lot more than it needs to --- we're already factoring ``n``, so instead of feeding those functions the integer forms of ``n``'s factors, we can instead pass ``ffac=True`` or ``gfac=True`` when ``dirconv`` is called and we will call ``divisors_factored(n)`` instead and feed those factored divisors into ``f`` or ``g`` as appropriate.  This optimization becomes more noticeable as the factoring becomes more difficult.

.. code:: python

    dirichletinverse(f)

Computes the Dirichlet inverse of the input function ``f``.  Mathematically, functions *f* such that *f*\ (1) = 0 have no Dirichlet inverses due to a division by zero.  This is reflected in this implementation by raising a ``ZeroDivisionError`` when attempting to evaluate ``dirichletinverse(f)(n)`` for any such ``f`` and any ``n``.  If ``f``\ (1) is neither 1 nor -1, then ``dirichletinverse(f)`` will return ``Fraction`` objects (as imported from the ``fractions`` module).

.. code:: python

    dirichletroot(f, r, val1)

Computes the ``r``\ :sup:`th` Dirichlet root of the input function ``f`` whose value at 1 is ``val1``.  More precisely, let ``f`` be a function on the positive integers, let ``r`` be a positive integer, and let ``val1``\ :sup:`r` = ``f``\ (1).  Then we return the unique function ``g`` such that ``f`` = ``g`` * ``g`` * ... * ``g``, where ``g`` appears ``r`` times and * represents Dirichlet convolution.  The values returned will be ``Fraction`` objects (as imported from the ``fractions`` module).

.. code:: python

    determinant(M)

Computes the determinant of a matrix via the Schur determinant identity.

.. code:: python

    discriminant(coefs)

Computes the discriminant of a polynomial.  The input list is ordered from lowest degree to highest --- i.e., ``coefs[k]`` is the coefficient of the *x*\ :sup:`k` term.  For low-degree polynomials, explicit formulae are used; for degrees 5 and higher, we compute it by taking the determinant (using this package's ``determinant`` function) of the Sylvester matrix of the input and its derivative.  This in turn is calculated by the Schur determinant identity.  Note that this has the effect of setting the discriminant of a linear polynomial to 1 (which is conventional) and that of a constant to 0.

.. code:: python

    egypt_short(n, d, terms=0, minden=1)

Generates all shortest Egyptian fractions for ``n``/``d`` using at least the indicated number of terms and whose denominators are all ≥ ``minden``.  No algorithm is known for this problem that significantly improves upon brute force, so this can take impractically long times on even modest-seeming inputs.

.. code:: python

    egypt_greedy(n, d)

The greedy algorithm for Egyptian fraction expansion; also called the Fibonacci-Sylvester algorithm.


Dependencies
------------

This package imports items from ``multiprocessing``, ``itertools``, ``fractions``, ``random``, ``math``, and ``heapq``.  These are all in the Python standard library.

We attempt to import ``mpz`` from ``gmpy2``, but this is purely for efficiency: if this import fails, we simply set ``mpz = int``.
