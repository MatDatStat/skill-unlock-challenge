# Operation: Skill Forge — the challenge

You found the branch. Nicely decoded.

You're looking at 60 tablet batches that went through a NIR (Near-InfraRed) probe on
the line, plus a lab reference moisture value for each batch — `data/nir_spectra.csv`.
15 wavelength channels (`wl_1000` … `wl_2400`), one row per batch.

This is a classic Process Analytical Technology (PAT) setup: can the fast, in-line
spectral measurement replace (or flag problems ahead of) the slow lab reference test?

## Your task

Work with your agent to:

1. **Build a calibration model.** Fit a regression of `moisture_pct_ref` on the
   wavelength channels (simple multiple linear regression is enough — no need for
   full PLS/chemometrics tooling unless you want to). Hold out a test split and report
   R² and RMSEP (root-mean-square error of prediction).

2. **Run a statistical process control check.** Plot/compute a Shewhart-style control
   chart on the batches in order (mean ± 3σ control limits) using the reference
   moisture values. Identify which batch(es), if any, fall outside the control limits.

3. **Produce a short report.** Have your agent write it out as a single, standalone
   **`report.html`** file — one file you can just double-click and open in a browser,
   with no separate image files or external dependencies (embed any figures directly
   in the HTML, e.g. as base64-encoded PNGs/SVGs or inline SVG). It should include:
   the model performance (R², RMSEP), a figure of predicted vs. reference moisture, the
   control chart figure with control limits drawn in, and the batch IDs you'd flag for
   an out-of-spec investigation.

## Hint

Stuck, or just want to move fast?

- **Calibration:** you don't need to hand-pick wavelengths — throw all 15 `wl_*`
  columns into a multiple linear regression against `moisture_pct_ref` and split
  off ~20% of the batches as a test set before scoring it.
- **Control chart:** compute the mean and standard deviation of `moisture_pct_ref`
  across all 60 batches, then flag any batch where the value falls outside
  `mean ± 3 × std`. That's the standard Shewhart / 3-sigma rule — no fancier
  statistics needed.
- Ask your agent to write this as a couple of small, named functions (load →
  fit/evaluate → flag-outliers) rather than one long script. It'll matter later.

## You're done when

You have a short list of batch IDs that are statistically out of control. If your
analysis is right, you'll have found exactly the batches this dataset was built to
hide.

## Unlocking the reward

`reward/reward.enc` is encrypted — solving the task correctly is the only way in.

Your unlock code is **the sum of the numeric part of every batch ID you flagged**.
For example, if you flagged `B006`, `B012`, and `B050`, your code would be
`6 + 12 + 50 = 68`.

Once you have your number, have your agent run (no extra tools needed beyond
`openssl`, which ships with git):

```
openssl enc -d -aes-256-cbc -pbkdf2 -in reward/reward.enc -pass pass:YOUR_CODE | tar -xz -C reward
```

Replace `YOUR_CODE` with your number. If it's wrong, decryption will fail outright —
there's no partial credit, just try your analysis again.

## A nudge, if you want it

You'll probably end up writing: a data-loading function, a preprocessing/regression
step, and a control-chart function. That's three reusable pieces of PAT/stats tooling
you just built from scratch. Worth remembering, if only your agent could remember it
for you...
