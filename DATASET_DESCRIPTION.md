# Sanctuary Soundscape Clock: Timed Underwater Soundscape Spectrograms from Eight U.S. National Marine Sanctuaries

## Overview

This dataset contains 1,600 mel-spectrogram images of 30-second underwater recordings from 160 hydrophone deployments at 28 sites in eight U.S. National Marine Sanctuaries, recorded between March 2019 and June 2022. Each recording carries two exact labels taken from the recorder clock: the local solar hour at the middle of the recording and the day of the year.

The recordings come from the Sanctuary Soundscape Monitoring Project (SanctSound), run by the NOAA Office of National Marine Sanctuaries and the U.S. Navy and archived at the NOAA National Centers for Environmental Information. They are U.S. Government works in the public domain. This release contains no raw audio, no calendar dates or clock times beyond the two labels, no file names and no coordinates beyond a rounded latitude; sites and deployments are identified only by opaque keys.

## Release At A Glance

- Raw files: 9
- Recordings (cases): 1,600, ten per deployment
- Deployments: 160; sites: 28; sanctuaries: 8 (Stellwagen Bank, Channel Islands, Florida Keys, Gray's Reef, Monterey Bay, Hawaiian Islands Humpback Whale, Olympic Coast, Papahanaumokuakea)
- Latitude: 20.0 to 48.5 degrees north
- Recording length: 30 seconds; spectrogram shape (60, 96)
- Prepared split: 129 deployments / 1,290 training recordings, 31 other deployments / 310 test recordings

## Raw File Structure

The uploaded ZIP is flat and contains exactly these files:

- `cases.csv`: one record per recording: `case_id`, `site_id`, `deployment_id`, `latitude`, `solar_hour`, `day_of_year`.
- `spectrograms.npz`: one uint8 array of shape (60, 96) per `case_id`.
- `test_deployments.txt`: the `deployment_id` values held out for the prepared test split, one per line.
- `LICENSE`: CC0 1.0 notice.
- `ATTRIBUTION.txt`: source and attribution text.
- `DATASET_CARD.md`: short scope summary.
- `DATASET_DESCRIPTION.md`: this document.
- `source_metadata.json`: provenance and processing parameters.
- `PACKAGE_MANIFEST.sha256`: SHA-256 checksum of every other raw file.

## Columns

### cases.csv

- `case_id` (string): opaque recording identifier, for example `c_3f9c1a7b2e`.
- `site_id` (string): opaque hydrophone-site key, for example `site_0b1c2d3e`.
- `deployment_id` (string): opaque deployment key, for example `dep_91e2c0d4`. A deployment is one continuous recorder placement at a site, typically three to five months long.
- `latitude` (number): site latitude in degrees north, rounded to 0.1.
- `solar_hour` (number): local solar hour at the middle of the recording, in [0, 24): the UTC clock hour plus site longitude divided by 15, modulo 24. Solar noon is near 12 at every site.
- `day_of_year` (integer): day of the year of the middle of the recording in UTC, 1 to 366.

### spectrograms.npz

- One array per `case_id`, shape (60, 96), dtype uint8.
- Rows run along the horizontal axis of the image; each row averages 0.5 seconds of short-time Fourier power.
- Columns are 96 mel bands between 20 Hz and 8 kHz, low to high.
- A value v means log10 mel power = v * 0.04 - 9 (values are rounded and clipped to 0 to 255).

## How The Spectrograms Are Made

- Audio is decoded from the archived recordings (48 kHz) and resampled to 16 kHz, mono.
- Power spectra use a 1,024-sample Hann window with a 160-sample hop, are mapped to 96 triangular mel bands (area-normalised) between 20 Hz and 8 kHz, averaged over 50 consecutive hops (0.5 seconds) and converted to log10.
- Each recording then receives a keyed gain offset drawn uniformly between -0.6 and +0.6 in log10 units (-6 to +6 dB), so absolute levels are not comparable between recordings.

## How The Recordings Are Drawn

- Every SanctSound deployment in the northern hemisphere with archived audio and recorded coordinates is used.
- Within each deployment, ten start times are drawn at keyed uniform random positions over the recorded audio, at least three hours apart. Because times are uniform over the deployment, solar hours are spread evenly over the day, and days of the year follow the deployment calendar.
- The label time is the exact position of the released 30 seconds in the archived file, read from the audio frames themselves.
- All draws and identifiers derive by HMAC-SHA256 from a secret held by the creator; the secret, the clock times beyond the labels and the archive file names are not released.

## Prepared Outputs

`prepare.py` writes a public directory and a private answer directory.

- public `train.csv` (1,290 rows) and `test.csv` (310 rows): `case_id`, `site_id`, `deployment_id`, `latitude`.
- public `train_labels.csv` (1,290 rows): `case_id`, `solar_hour`, `day_of_year`.
- public `sample_submission.csv` (310 rows): the same columns, from a loudness rule.
- public `spectrograms.npz`: the arrays of every training and test recording.
- public `LICENSE`.
- private `answers.csv` (310 rows): the solar hour and day of year of the test recordings. It has the same columns as `sample_submission.csv`.

The test split is the deployments listed in `test_deployments.txt`, about one fifth of the deployments of each sanctuary. The preparation self-check verifies that deployments do not cross the split, that ids agree across files and that no public column is constant.

## Known Limitations

- **Weak signal per recording.** Thirty seconds of sound carries limited information about time; many recordings, for example quiet ones, are nearly uninformative.
- **Clustered seasons.** Each deployment covers a few months, so days of the year are correlated within a deployment and the test split's calendar coverage depends on 31 deployments.
- **Sites shared across the split.** Every sanctuary and most sites appear in both splits; transfer is across deployments, seasons and years rather than to new locations.
- **Solar hour approximation.** The solar hour uses longitude only and ignores the equation of time (up to about 16 minutes).
- **Reduced bandwidth.** Spectrograms stop at 8 kHz.

## Provenance And License

Source: NOAA Office of National Marine Sanctuaries and U.S. Navy, Sanctuary Soundscape Monitoring Project (SanctSound) raw passive acoustic data, NOAA National Centers for Environmental Information, https://doi.org/10.25921/saca-sp25. U.S. Government works in the public domain. This derived release is dedicated to the public domain under CC0 1.0.
