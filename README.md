# Timed Underwater Soundscape Spectrograms

A derived dataset of 1,600 mel-spectrogram images (60 x 96) of 30-second underwater recordings from 160
moored hydrophone deployments at 28 sites, recorded between March 2019 and June 2022. Each recording is
labelled with the local solar hour and the day of year read from the recorder clock.

The prepared benchmark split is 1,050 training recordings from 105 deployments and 550 test recordings
from 55 other deployments. Each recording is released with three candidate clock settings, one of which
is the true one; the three are a cyclic ring, one setting shifted by k times (8 hours, 122 days) for
k = 0 to 2, so the candidate set is the same whichever member is true.

- **Files:** `DATASET_DESCRIPTION.md` (the full data card), `LICENSE`.
- **Licence of this derived release:** CC0 1.0.
- **Provenance:** derived from U.S. Government passive acoustic recordings in the public domain, archived
  at the NOAA National Centers for Environmental Information. No raw audio, timestamps, recorder
  locations or original filenames are redistributed here; the released spectrograms are lossy
  low-resolution derivatives carrying a keyed gain offset.
