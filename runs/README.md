# runs/

One self-contained folder per HIL run: `runs/<test>_<YYYYMMDD>/`, created by
`hil/tools/hil_new_run.sh`. Each holds `RESULTS.md` (verdict per criterion) and
`run_manifest.json`, plus the evidence they cite. Format: `hil/README.md` §3–5.
Logs and CSVs are committed; media is not (sha256 in the manifest).
