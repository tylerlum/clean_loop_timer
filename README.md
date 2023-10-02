# loop_timer

Easy-to-use timer for profiling complex loops in dataset generation and neural network training

# Installing

Install:

```
pip install loop_timer
```

# Usage

In this library, we have `SectionTimer` and `LoopTimer`. `SectionTimer` measures the time it takes to run a section of code using a `with` statement. `LoopTimer` uses multiple `SectionTimer` objects to measure multiple sections within a loop, aggregates the results, and prints out the results in a useful format. In particular, it prints out the timing both in absolute time and fraction of the total time. It also prints out both the most time to run the section of code in the most recent loop, as well as aggregates the timing of the same section over multiple loops. This is very useful for situations in which a section of code is very slow on first run but is cached in subsequent runs, so we want to see both the most recent time and the aggregated time.

Example:

```
python timers.py
```

Full example:

```
from tqdm import tqdm
import time

from loop_timer import LoopTimer

loop_timer = LoopTimer()
pbar =  tqdm(range(100))
for i in pbar:
    with loop_timer.add_section_timer("test"):
        if i < 3:
            time.sleep(1.0)
        else:
            time.sleep(0.1)

    with loop_timer.add_section_timer("test2"):
        time.sleep(0.3)

    section_times_df = loop_timer.get_section_times_df()
    loop_timer.pretty_print_section_times(df=section_times_df)
    pbar.set_description(
        " | ".join(
            [
                f"{section_times_df['Section'].iloc[j]}: {section_times_df['Most Recent Time (ms)'].iloc[j]:.0f}"
                for j in range(len(section_times_df))
            ]
        )
    )
```

Example output:

```
(loop_timer_env) ➜  loop_timer git:(main) ✗ python timers.py
  0%|                                                                                                                                          | 0/100 [00:00<?, ?it/s]|    | Section   |   Most Recent Time (ms) |   Most Recent Time (%) |   Sum Time (ms) |   Sum Time (%) |
|---:|:----------|------------------------:|-----------------------:|----------------:|---------------:|
|  0 | test      |                  1000.7 |                   76.9 |          1000.7 |           76.9 |
|  1 | test2     |                   300.4 |                   23.1 |           300.4 |           23.1 |
test: 1001 | test2: 300:   1%|█                                                                                                        | 1/100 [00:01<02:11,  1.32s/it]|    | Section   |   Most Recent Time (ms) |   Most Recent Time (%) |   Sum Time (ms) |   Sum Time (%) |
|---:|:----------|------------------------:|-----------------------:|----------------:|---------------:|
|  0 | test      |                  1000.4 |                   76.9 |          2001.2 |           76.9 |
|  1 | test2     |                   300.4 |                   23.1 |           600.8 |           23.1 |
test: 1000 | test2: 300:   2%|██                                                                                                       | 2/100 [00:02<02:08,  1.31s/it]|    | Section   |   Most Recent Time (ms) |   Most Recent Time (%) |   Sum Time (ms) |   Sum Time (%) |
|---:|:----------|------------------------:|-----------------------:|----------------:|---------------:|
|  0 | test      |                  1001.1 |                   76.9 |          3002.3 |           76.9 |
|  1 | test2     |                   300.6 |                   23.1 |           901.4 |           23.1 |
test: 1001 | test2: 301:   3%|███▏                                                                                                     | 3/100 [00:03<02:07,  1.31s/it]|    | Section   |   Most Recent Time (ms) |   Most Recent Time (%) |   Sum Time (ms) |   Sum Time (%) |
|---:|:----------|------------------------:|-----------------------:|----------------:|---------------:|
|  0 | test      |                   100.2 |                   25.0 |          3102.5 |           72.1 |
|  1 | test2     |                   300.2 |                   75.0 |          1201.6 |           27.9 |
test: 100 | test2: 300:   4%|████▏                                                                                                     | 4/100 [00:04<01:31,  1.05it/s]|    | Section   |   Most Recent Time (ms) |   Most Recent Time (%) |   Sum Time (ms) |   Sum Time (%) |
|---:|:----------|------------------------:|-----------------------:|----------------:|---------------:|
|  0 | test      |                   100.1 |                   25.0 |          3202.6 |           68.1 |
|  1 | test2     |                   300.6 |                   75.0 |          1502.2 |           31.9 |
test: 100 | test2: 301:   5%|█████▎                                                                                                    | 5/100 [00:04<01:11,  1.32it/s]|    | Section   |   Most Recent Time (ms) |   Most Recent Time (%) |   Sum Time (ms) |   Sum Time (%) |
|---:|:----------|------------------------:|-----------------------:|----------------:|---------------:|
|  0 | test      |                   100.1 |                   25.0 |          3302.7 |           64.7 |
|  1 | test2     |                   300.4 |                   75.0 |          1802.6 |           35.3 |
test: 100 | test2: 300:   6%|██████▎                                                                                                   | 6/100 [00:05<00:59,  1.57it/s]|    | Section   |   Most Recent Time (ms) |   Most Recent Time (%) |   Sum Time (ms) |   Sum Time (%) |
|---:|:----------|------------------------:|-----------------------:|----------------:|---------------:|
|  0 | test      |                   100.1 |                   25.0 |          3402.8 |           61.8 |
|  1 | test2     |                   300.4 |                   75.0 |          2103.0 |           38.2 |
test: 100 | test2: 300:   7%|███████▍                                                                                                  | 7/100 [00:05<00:52,  1.78it/s]|    | Section   |   Most Recent Time (ms) |   Most Recent Time (%) |   Sum Time (ms) |   Sum Time (%) |
|---:|:----------|------------------------:|-----------------------:|----------------:|---------------:|
|  0 | test      |                   100.1 |                   25.0 |          3503.0 |           59.3 |
|  1 | test2     |                   300.4 |                   75.0 |          2403.4 |           40.7 |
```

![loop_timer](https://github.com/tylerlum/loop_timer/assets/26510814/2150e7e1-47b9-405a-b83f-b6b705bff263)
