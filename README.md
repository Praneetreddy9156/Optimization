# Opti AI Agent 🚀

Opti AI Agent is a small terminal app that solves classic operations research problems (transportation, assignment, and two-machine job sequencing) and then asks a local LLM to explain the results in plain language.

Everything runs on your own machine. It uses no cloud APIs, needs no account, and costs nothing. If Ollama isn't available, the solvers still work and you only lose the AI summary.

## ✨ Features

- **Transportation problem:** runs the Northwest Corner, Least Cost, and Vogel's Approximation (VAM) methods on the same input, compares their total costs side by side, and prints each method's allocation table. Unbalanced input is rejected with a clear error.
- **Assignment problem:** finds the minimum-cost one-to-one assignment using SciPy's `linear_sum_assignment` (the same problem the Hungarian method solves) and shows which worker gets which job.
- **Job sequencing (Johnson's Rule):** finds the order of *n* jobs on 2 machines that minimizes the total elapsed time (makespan).
- **Local AI summary:** sends the results to `qwen2.5:3b` running in [Ollama](https://ollama.com) and prints a short professional summary in the terminal.

## 🛠️ Tech Stack

- **Language:** Python 3.8+
- **Math:** NumPy, SciPy
- **AI:** Ollama Python client with the `qwen2.5:3b` model
- **Interface:** plain command-line menu that runs in any terminal

## Project Structure

```
main.py           # menu, input handling, results display, Ollama call
solver.py         # Northwest Corner, Least Cost, VAM, assignment, Johnson's Rule
requirements.txt  # Python dependencies
```

## 🚀 Quick Start

**1. Install Ollama** from [ollama.com/download](https://ollama.com/download). This is a separate app; `pip install ollama` only installs the Python client.

**2. Download the model** (about 2 GB; roughly 4 GB of free RAM recommended):

```bash
ollama pull qwen2.5:3b
```

**3. Make sure Ollama is running.** On Windows and macOS the Ollama desktop app starts the server automatically. On Linux, or if you aren't using the desktop app, start it yourself:

```bash
ollama serve
```

**4. Get the code and install the Python dependencies:**

```bash
git clone <your-repo-url>
cd <repo-folder>
pip install -r requirements.txt
```

**5. Run the app:**

```bash
python main.py
```

## Usage

Choose a problem type from the menu, then enter the data when prompted.

| Option | Input |
|---|---|
| 1. Transportation | A 4×4 cost matrix (4 rows of 4 numbers separated by spaces), then 4 supply values and 4 demand values (spaces or commas). Rows are sources S1–S4; columns are destinations D1–D4. |
| 2. Assignment | A 4×4 cost matrix (4 rows of 4 numbers separated by spaces). Rows are workers; columns are jobs. |
| 3. Job sequencing | All Machine 1 processing times on one line, then all Machine 2 times on one line, with the same number of jobs on each |

### Example: transportation problem

```
Enter choice (1, 2, or 3): 1

Enter your 4x4 Matrix row by row (space-separated values):
Row 1 (4 numbers): 19 30 50 10
Row 2 (4 numbers): 70 30 40 60
Row 3 (4 numbers): 40 8 70 20
Row 4 (4 numbers): 60 20 40 30
Enter Supply vector (4 numbers): 10 15 20 15
Enter Demand vector (4 numbers): 12 18 15 15

⏳ Running Transportation Heuristics...

--------------------------------------------------
📊 TRANSPORTATION RESULTS SUMMARY
--------------------------------------------------
-> Northwest Corner              : 2,260.00
-> Least Cost Method             : 1,694.00
-> Vogel's Approximation (VAM)   : 1,464.00
--------------------------------------------------

Northwest Corner allocation:
              D1        D2        D3        D4
  S1          10         -         -         -
  S2           2        13         -         -
  S3           -         5        15         -
  S4           -         -         -        15

Least Cost Method allocation:
...

Vogel's Approximation (VAM) allocation:
              D1        D2        D3        D4
  S1          10         -         -         -
  S2           -         -        15         -
  S3           2        18         -         -
  S4           -         -         -        15

🤖 [Opti AI Agent is evaluating locally via Qwen2.5:3b...]

<a short summary from the local model appears here>
```

For this input, VAM's starting solution costs about 35% less than Northwest Corner's.

If total supply and total demand differ, the app stops before solving:

```
Error: Total supply (60.00) must equal total demand (55.00).
Use one row or column as a zero-cost dummy to balance the problem, then try again.
```

### Example: assignment

With the cost matrix `9 2 7 8` / `6 4 3 7` / `5 8 1 8` / `7 6 9 4`:

```
-> Worker 1 -> Job 2          : 2.00
-> Worker 2 -> Job 1          : 6.00
-> Worker 3 -> Job 3          : 1.00
-> Worker 4 -> Job 4          : 4.00
-> Optimal Assignment Cost    : 13.00
```

### Example: job sequencing

With Machine 1 times `5 1 9 3 10` and Machine 2 times `2 6 7 8 4`:

```
-> Optimal Sequence           : Job 2 -> 4 -> 3 -> 5 -> 1
-> Total Elapsed Time         : 30.00
```

## Limitations

- **Transportation and assignment only accept 4×4 matrices.** The size is fixed in `main.py`.
- **Transportation problems must be balanced** (total supply = total demand). The app doesn't add a dummy row or column for you, so balance the problem before entering it, for example by using one row or column as a dummy with zero costs.
- **The three transportation methods give starting solutions, not a proven optimum.** There is no MODI or stepping-stone optimality test yet.
- **Assignment only minimizes.** There is no maximization (profit) mode.
- **Job sequencing supports exactly 2 machines** and reports the makespan only, not machine idle time.
- **The AI summary only describes the computed numbers.** It doesn't change the results, and a 3B model can occasionally misstate a figure, so treat the printed results table as the source of truth.

## Troubleshooting

| Problem | Fix |
|---|---|
| `Model 'qwen2.5:3b' is not installed.` | Ollama is running but the model isn't downloaded. Run `ollama pull qwen2.5:3b`. |
| `Could not connect to Ollama.` | Ollama isn't running. Open the Ollama app or run `ollama serve`. |
| `ollama serve` says the address is already in use | Ollama is already running (usually the desktop app). You don't need to start it again. |
| `Error: Total supply (...) must equal total demand (...).` | The transportation problem is unbalanced. Make the totals match, using a zero-cost dummy row or column if needed. |
| `Error: Please enter exactly 4 numbers.` | Matrix rows must contain exactly 4 numbers separated by spaces. Commas are only accepted for supply and demand. |

## License

This project does not have a license yet.

---

Built for engineers, students, and operations researchers who want a fast, fully offline optimization assistant.
