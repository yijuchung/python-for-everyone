# Chapter 20 Practice Bank: Multithreading

A deeper set of practice problems, organized by topic, on top of the main
`exercises/` folder — including scenario-based problems written in the
same style you'll see in real interviews. This is the chapter where
`threading`, `threading.Lock`, `threading.local`, and
`concurrent.futures.ThreadPoolExecutor` and `ProcessPoolExecutor` become allowed, on top of
everything from Chapters 1-19. Every "wait" here uses `time.sleep()` to
stand in for a slow network or disk operation — no real network access
or application-data file I/O is needed. The process-pool tasks do actual
CPU work instead of sleeping. No import beyond
`json`/`os`/`re`/`math`/`datetime`/`random`/`csv`/`time`/`threading`/`concurrent.futures`.

## How to run

```bash
cd practice
python3 starter.py
```

Use `python` instead of `python3` if that is your local command. Run the
saved script, not a notebook, interactive shell, or browser playground:
process workers need an importable module. Define workers at module
level and put all demonstration calls inside `main()`, which is called
only under `if __name__ == "__main__":`. Both files use this layout so
fresh workers do not rerun the earlier threading demos during import.

## Topic 1: What a Thread Is, and I/O-Bound vs. CPU-Bound

1. Classify a task as I/O-bound or CPU-bound.
2. Estimate a sequential total time from a list of task durations.
3. Estimate a threaded total time from the same list.
4. **Debug the Code:** fix a classification that had I/O-bound and CPU-bound backwards.
5. **Scenario — Explaining Why Threading Helps Downloads:** write `explain_why_threading_helps_downloads()`.
6. **Scenario — Interview Prep:** explain the difference between I/O-bound and CPU-bound work.

## Topic 2: Creating and Starting Threads

1. Create, start, and join threads appending to a shared list.
2. Run two named tasks on separate threads.
3. Count how many threads were started and joined.
4. **Debug the Code:** fix threads joined immediately after starting, removing all concurrency.
5. **Scenario — Running Any Task Concurrently:** write a reusable `run_tasks_concurrently()`.
6. **Scenario — Interview Prep:** explain why every thread should be started before any are joined.

## Topic 3: The GIL

Assume one GIL-enabled CPython interpreter and pure-Python CPU work for
the simple classifiers; include the exceptions in the interview answer.

1. Identify blocking waits that release the GIL. A CPU loop is not a wait, but still periodically hands off the GIL.
2. Decide whether threading would help a given kind of work under those assumptions.
3. **Debug the Code:** fix a claim that Python threads sharing an enabled GIL run bytecode fully in parallel.
4. **Scenario — Explaining the GIL to a Teammate:** explain turn-taking rather than starvation, overlapping I/O waits, and separate process interpreters.
5. **Scenario — Interview Prep:** include native code that releases the GIL and optional free-threaded CPython (experimental in 3.13, supported in 3.14), without dropping synchronization.

## Topic 4: Race Conditions

1. Decide whether a situation has race-condition risk.
2. Run an unsafe shared counter across threads and observe the (unreliable) result.
3. List the hidden steps behind `counter += 1`.
4. **Debug the Code:** fix a claim that the GIL fully prevents race conditions.
5. **Scenario — Diagnosing a Flaky Counter Bug:** write `diagnose_flaky_counter_bug()`.
6. **Scenario — Interview Prep:** explain why race conditions are hard to catch during testing.

## Topic 5: `threading.Lock`

1. Fix a shared counter using `threading.Lock`.
2. Safely append to a shared list from multiple threads.
3. List the steps of the `with lock:` pattern.
4. **Debug the Code:** fix code that created a lock but never actually used it.
5. **Scenario — A Thread-Safe Logger:** write `build_thread_safe_logger()`.
6. **Scenario — Interview Prep:** explain the tradeoff of using a lock.

## Topic 6: Thread Safety Patterns

1. Combine independent per-thread results with no lock needed.
2. Use `threading.local()` for a per-thread counter.
3. Sum independent ranges computed on separate threads.
4. **Debug the Code:** harden a shared-dict write with a lock.
5. **Scenario — Parallel Word Count:** write `parallel_word_count(chunks)`.
6. **Scenario — Interview Prep:** explain shared-nothing design vs. locking.

## Topic 7: `ThreadPoolExecutor`

1. Use `.map()` for ordered concurrent results.
2. Use `.submit()` + `as_completed()` for results as they finish.
3. Write a reusable pool-running helper.
4. **Debug the Code:** fix a pool that was never used as a context manager.
5. **Scenario — Fetching Weather for Several Cities Concurrently:** write `fetch_all_weather(cities)`.
6. **Scenario — Interview Prep:** explain what `ThreadPoolExecutor` offers over raw `Thread` objects.

## Topic 8: `ProcessPoolExecutor`

1. Write `choose_pool(work_type)` for GIL-enabled CPython: thread pool for I/O, process pool for independent CPU-heavy Python jobs; raise `ValueError` for an unknown label.
2. Write the module-level `count_primes(limit)` worker and `count_all_primes(limits)` using a two-worker process pool and `.map()`. `[10, 20, 30]` should produce `[4, 8, 10]` in input order; `[]` should produce `[]`.
3. Write `collect_prime_counts(limits)` with `.submit()`, `as_completed()`, and `.result()`. Return sorted `(limit, count)` results and sorted `(limit, message)` failures. For `[10, -1, 20]`, report `[(10, 4), (20, 8)]` and `[(-1, 'limit must be non-negative')]`; do not hide a failed task behind a count of zero.
4. **Debug the Code:** replace an unpicklable lambda worker with the existing module-level `triple_val` function. Run the repaired call only from `main()`.
5. **Scenario — Missing Report Results:** explain why a process worker's list updates do not change the parent's list, and why returning values is the right design.
6. **Scenario — Interview Prep:** explain why tiny CPU jobs can be slower in a process pool, how to measure total cost, and why `sleep()` is not a CPU benchmark.

The small inputs keep expected results easy to check; they are not a
speedup benchmark. Worker functions, arguments, and results must be
picklable, and a `threading.Lock` is not a process-sharing mechanism.

## Checking your work

Compare your output against `solution.py`. Your exact wording doesn't
need to match — the goal is that your program runs without errors and
does what each TODO asks. One exception: Topic 4's race-condition demo
(4.2) and Topic 5's "broken" debug case print a number that varies
between runs by design — that's the whole point of those two tasks.
Every other task's output is exactly reproducible.
