[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004551-blue)](https://doi.org/10.82901/nemar.on004551)

This dataset was curated for publication as part of the manuscript in Sakakura et al. (in preparation). 
It contains iEEGs collected from 114 individuals during slow wave sleep. 
The available Matlab code can be found at https://github.com/kaz1126/MI_HFO.
The iEEG coordinate system employed in this dataset is MNI305.

## Channel names follow the data headers (18 Oct 2026)

In 125 recordings the channel list of channels.tsv did not equal the data-file header (names, order or count). In the 125 with a different channel count, `mne_bids.read_raw_bids` warned and kept the header names, so the channels.tsv types and bad-channel status reached only the channels whose names happen to be identical; a reader that applies the channels.tsv rows by position gives wrong labels wherever the two lists are not aligned. Since 18 Oct 2026 every channels.tsv lists exactly the header channels, in header order, with the header names (MNE names: duplicated header labels numbered -0, -1).

Rows were matched to header channels BY NAME, never by position (exact; then case/whitespace; then the recorder prefix 'EEG '/'POL ', '_' and zero padding; then a trailing '-<reference>' token). Matched rows keep all their columns (type, units, status, ...); 14476 rows took the header name (the header labels carry 'POL ' prefix, 'EEG ' prefix + '-Ref' suffix).

2043 header channels had no channels.tsv row (125 files) and got a new row: type from the name (ECG/EMG/EOG/trigger) or from the other contacts of the same shaft, else MISC; units from the header; status n/a. Examples: `sub-001_ses-01_task-sleep_run-01_ieeg.edf`: POL E, POL DC01, POL DC02, POL DC03; `sub-002_ses-01_task-sleep_run-01_ieeg.edf`: POL E, POL DC01, POL DC02, POL DC03; `sub-003_ses-01_task-sleep_run-01_ieeg.edf`: POL E, POL DC01, POL DC02, POL DC03.

1203 channels.tsv rows had no channel in the data file (56 files) and were removed; they are kept verbatim under `sourcedata/channels-removed-2026-10-18/` (same relative path). Examples: `sub-001_ses-01_task-sleep_run-01_ieeg.edf`: B43, B44, B45, B46, B47; `sub-003_ses-01_task-sleep_run-01_ieeg.edf`: B15, B16, B17, B18, B19; `sub-007_ses-01_task-sleep_run-01_ieeg.edf`: B41, B42, B43, B44, B45.

125 files listed the channels in another order than the header; rows now follow the header order.

Applied by position, the former rows would have given wrong labels to 13719 channels in 125 files (rows missing, extra or out of order before that position).

electrodes.tsv is unchanged: it names the contacts (e.g. `A1`), while channel names follow the recorder (e.g. `A1-AV`); to attach positions in MNE, map channel names to contact names first, e.g. `raw.rename_channels(lambda n: re.sub(r'-[A-Za-z0-9]+$', '', n))`.

## Electrode locations (19 Oct 2026; revised 22 Oct 2026)

Add coordsystem.json MNI305/mm next to 124 electrodes.tsv. Evidence: Wayne State (Asano/Kanno/Kochi) deposits use FreeSurfer-standard (MNI305/fsaverage) electrode coordinates; MNI305 is a millimetre space by definition. No OpenNeuro S3 version of this dataset ever held a coordsystem.json (all versions listed 2026-10-19). README: "The iEEG coordinate system employed in this dataset is MNI305." Paper PMC10575956 (Methods, MRI): "normalize the electrode locations to the standardized FSaverage brain surface".

Primary source: this dataset's own README (line 6 of README.md on main, nemarDatasets/on004551@c4c035f6), quoted above.

Reproducible check (22 Oct 2026): for each of the 124 electrodes.tsv, every contact's x/y/z was compared (to 0.001 mm) with every electrodes.tsv of other deposits from the same group at pinned nemarDatasets commits (on004859@db21ebb2, on005007@b59363aa, on005545@948f2825, on005931@cbd529e5, on006107@b37dcbfc, on006233@396692f0, on006910@9eb96ebd, on006914@1956b035, on007118@8f2c41fa, on007119@c099745b, on007120@66296ebd). 101 files have all contacts identical to one file of another deposit: 68 to a deposit whose coordsystem.json declares fsaverage/mm (on007118, on007119, on007120) and 66 to a deposit whose README states MNI305/MNI-305 (on005545, on006233, on006910, on006914); a file can match both. The other 23 match partly (11 with at least 89 % of contacts identical, 0 with 50-89 %, 12 below 50 %, 0 none).

Limitation: these sidecars declare the frame and units only. electrodes.tsv is unchanged and no coordinate value changed. Positions do not attach to channels in mne_bids or EEGDash, because the channel names (data-header labels, e.g. prefixed labels or bipolar derivations) differ from the contact names in electrodes.tsv; a channel-to-contact mapping is not part of this change. This dataset mirrors OpenNeuro; the same change belongs upstream.
