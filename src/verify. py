#!/usr/bin/env python3
"""
Adaptive hyperbolic verifier for the Erdős–Straus conjecture
on the six difficult residue classes modulo 840.
"""

import argparse
import concurrent.futures
import math
import os
import time
from typing import List, Optional, Tuple


def get_difficult_primes(limit: int) -> List[int]:
    """Generate all primes p <= limit that lie in the six difficult classes mod 840."""
    print(f"Generating primes up to {limit:,} ...")
    t0 = time.time()

    difficult_residues = {1, 121, 169, 289, 361, 529}
    sieve = bytearray([1]) * (limit + 1)
    sieve[0] = sieve[1] = 0

    for i in range(2, math.isqrt(limit) + 1):
        if sieve[i]:
            sieve[i * i : limit + 1 : i] = bytearray(len(range(i * i, limit + 1, i)))

    primes = [
        p for p in range(3, limit + 1)
        if sieve[p] and (p % 840) in difficult_residues
    ]

    print(f"Found {len(primes):,} difficult primes in {time.time() - t0:.2f}s.")
    return primes


def _search_hyperbola(
    n: int, max_k: int, max_g: int, max_u: int
) -> Optional[Tuple[int, int, int, int, int, int, int]]:
    """Search for a solution inside the given parameter bounds."""
    for k in range(1, max_k + 1):
        for g in range(k, max_g + 1, k):
            N = 4 * g * k * n + 1
            limit_A = math.isqrt(N) + 1

            for u in range(1, max_u + 1):
                A = 4 * g * u - 1
                if A > limit_A:
                    break

                if N % A == 0:
                    B = N // A
                    if (B + 1) % (4 * g) == 0:
                        v = (B + 1) // (4 * g)

                        if math.gcd(u, v) == 1:
                            # Note: (g * u * v) is guaranteed to be divisible by k
                            # since g is generated in steps of k.
                            x = (g * u * v) // k
                            y = g * u * n
                            z = g * v * n

                            # Exact rational verification: 4/n == 1/x + 1/y + 1/z
                            if (y * z + x * z + x * y) * n == 4 * x * y * z:
                                return (k, g, u, v, x, y, z)
    return None


def solve_single_prime_adaptive(n: int):
    """
    Two-stage resolver:
    1. Fast primary bounds (covers >99.99 % of cases)
    2. Extended bounds for the rare hard exceptions
    """
    # Primary pass
    res = _search_hyperbola(n, max_k=30, max_g=120, max_u=3000)
    if res:
        return (n, True, False, res)

    # Extended pass
    res_ext = _search_hyperbola(n, max_k=50, max_g=1000, max_u=15000)
    if res_ext:
        return (n, True, True, res_ext)

    return (n, False, False, None)


def process_chunk(chunk: List[int]):
    return [solve_single_prime_adaptive(p) for p in chunk]


def run_test_adaptive(limit: int = 10_000_000):
    primes = get_difficult_primes(limit)
    total = len(primes)
    if total == 0:
        return

    num_workers = os.cpu_count() or 4
    chunk_size = max(100, total // (num_workers * 4))
    chunks = [primes[i : i + chunk_size] for i in range(0, total, chunk_size)]

    print(f"Starting adaptive verification on {num_workers} cores ...")
    t0 = time.time()

    solved_primary = 0
    solved_extended = 0
    failed = []
    extended_details = []

    with concurrent.futures.ProcessPoolExecutor(max_workers=num_workers) as executor:
        futures = [executor.submit(process_chunk, c) for c in chunks]

        processed = 0
        for future in concurrent.futures.as_completed(futures):
            chunk_res = future.result()
            for p, success, was_extended, data in chunk_res:
                if success:
                    if was_extended:
                        solved_extended += 1
                        extended_details.append((p, data[:4]))
                    else:
                        solved_primary += 1
                else:
                    failed.append(p)

            processed += len(chunk_res)
            pct = processed / total * 100
            print(
                f"\rProcessed: {processed:,}/{total:,} ({pct:.2f}%) | "
                f"Primary: {solved_primary:,} | Extended: {solved_extended:,}",
                end="",
                flush=True,
            )

    elapsed = time.time() - t0
    total_solved = solved_primary + solved_extended
    coverage = total_solved / total * 100

    print("\n\n" + "=" * 65)
    print(f"FINAL RESULTS FOR limit = {limit:,}")
    print("=" * 65)
    print(f"Total difficult primes tested : {total:,}")
    print(f"Solved in PRIMARY bounds      : {solved_primary:,}")
    print(f"Solved in EXTENDED bounds     : {solved_extended:,}")
    print(f"Total solved                  : {total_solved:,}")
    print(f"Unsolved                      : {len(failed):,}")
    print(f"Coverage                      : {coverage:.6f}%")
    print(f"Time                          : {elapsed:.2f} s")
    print("=" * 65)

    if extended_details:
        print(f"\nExamples that needed extended bounds ({len(extended_details)}):")
        for p, params in extended_details[:15]:
            print(f"  n = {p} -> (k,g,u,v) = {params}")

    if failed:
        print("\nUnsolved primes (first 30):")
        print(failed[:30])


if __name__ == "__main__":
    parser = argparse.ArgumentParser(
        description="Erdős-Straus Hyperbolic Adaptive Verifier"
    )
    parser.add_argument(
        "--limit",
        type=int,
        default=10_000_000,
        help="Upper limit for prime generation (default: 10,000,000)",
    )
    args = parser.parse_args()

    run_test_adaptive(args.limit)
