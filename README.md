# SPHEREx Explorer

Explore actual SPHEREx infrared cutouts over time, select a source, inspect measurement quality, and compare spectra, brightness and apparent positions. All physics calculations retain the provenance and availability of their required inputs.

## Run locally

```powershell
Set-Location "$env:USERPROFILE\spherex-viewer"
.\.venv\Scripts\python.exe -m streamlit run .\app.py
```

For a fresh environment, create `.venv` with Python3.13 and install `requirements.txt`. To restart an older running version, press Ctrl+C in its terminal first.

## Screens

- **Sky image:** individual FITS images, actual dates, wavelength estimates and original-data downloads.
- **Time player:** north-up/east-left alignment, shared brightness stretch, playback controls and explicit missing coverage. Quality masks are applied before reprojection. Approximate image-sharpness matching and field-star registration checks protect comparison previews. The stack uses a robust asinh display and preserves physical values on hover.
- **Source measurements:** native-image aperture fluxes, errors, rejection reasons, spectrum, wavelength/time coverage, comparable-band light curve, relative magnitudes, tentative position track and conditional period search. Optional same-band reference-star correction rejects unstable or overlapping references.
- **Physical estimates:** catalogue-assisted distance, velocity and luminosity, a conditional thermal fit, and known-orbit calculations. Catalogue quality failures are prominently flagged; missing values are never invented.
- **Advanced graphs:** shared-reference brightness differences/covariance, tentative motion vector, Fnu/Flambda conversion, supplied-line Doppler illustration, known-orbit position/speed versus time, uploaded spectral-template comparison, and guarded five-parameter fitting of separately calibrated astrometry.
- **Sky context:** optional CDS Aladin map with WISE/2MASS/DSS imagery and Gaia DR3 catalogue overlay. These external survey views have their own observing epochs.
- **Target validation:** actual evidence for all five challenge targets, including coadded images, dated samples when retrievable, flux-quality outcomes, catalogue provenance and archive failures.
- **Observation coverage/data:** dates, detectors, metadata and CSV export.

Open **Five challenge targets** to select a named target. Fast-moving source coordinates are propagated from their catalogue epoch. The challenge-target measurement option follows supplied proper motion at each exposure date, while keeping source association and quality checks explicit. It defaults to forced-position photometry; enabling centroid following is a separate choice.

## Importing higher-quality measurements

The Source measurements tab can import a calibrated spectrophotometry CSV exported by the official IRSA tool. Mandatory columns are `wavelength`/`wavelength_um`, `flux`/`flux_ujy`, `flux_err`/`flux_err_ujy`, and `mjd`, in μm, μJy, μJy and MJD days. Optional columns include `lambda_width`, `det_id`, `flags`, `ra`, and `dec`. Record its provenance and confirm the units/source. Quality-flagged rows remain withheld. The app does not silently label an imported table as its own aperture measurements.

Advanced astrometric fits require separately calibrated, reference-corrected positions and documented errors. Uploading a file does not independently prove those calibrations. Relative parallax solutions are not used as absolute distances.

## Validation status

Read `data/targets/validation.md` and the in-app report for the latest actual outcomes. At the recorded run, real DR1 coadded spectral context was retrieved for all five targets. Dated image samples were retrieved for all five targets after adding direct SIA parsing and using verified saved metadata. Epsilon Indi B uses a QR3-only sample, explicitly labelled as partial release coverage. Intermittent SIA/TAP failures are retained in the provenance and are not interpreted as non-detections. Coadded wavelength layers are never played as observing dates.

The five required targets are Barnard's Star, Epsilon Indi B(Ba/Bb blend), WISE0855−0714, RW Aurigae and R Draconis. Epsilon Indi B's retrieved Gaia solution has a poor single-star-fit quality metric; automatic distance use is withheld. RW Aur's retrieved parallax fails the chosen reciprocal-distance signal-to-noise gate. The report records the exact retrieved values and sources.

Compact derived copies of fourteen real NASA demonstration cutouts are packaged under `data/demo_cutouts`, with a provenance manifest. They support the initial saved region without a live search. That demonstration region is labelled separately from the five required targets. Packaged demo/target samples omit the large EPSF extension; IMAGE, FLAGS, VARIANCE, ZODI and WCS-WAVE arrays and wavelength lookups are verified unchanged. Original archive URLs and per-extension hashes are retained in data/COMPACT_SAMPLE_PROVENANCE.json. Original full downloads remain in the local archive/cache. The app does not use these compact samples for spatial ePSF fitting.

## Reproduce checks

```powershell
.\.venv\Scripts\python.exe -m unittest test_sequence.py test_advanced_analysis.py test_portal.py
.\.venv\Scripts\python.exe .\test_physics.py
.\.venv\Scripts\python.exe .\test_photometry.py
.\.venv\Scripts\python.exe .\test_reliability.py
.\.venv\Scripts\python.exe .\validate_targets.py --skip-context
```

Tests use synthetic numerical fixtures only for known-answer verification. Synthetic observations are not displayed as NASA data. The validation command makes real archive requests and preserves prior verified coadded context when `--skip-context` is used.

## Scientific limits

Image-based source fluxes remain preliminary aperture measurements. There is no spatial ePSF fitting/deblending or full calibrated astrometric/interpolation-covariance error budget. Approximate common-Gaussian smoothing uses reported median PSF widths and is labelled accordingly. The official IRSA spectrophotometry tool is the supported path for calibrated PSF-model measurements.

Wavelength estimates from WCS-WAVE are for visualization. Detector identity alone does not guarantee equal wavelength. Light curves and comparison stacks enforce approximate centre/bandwidth matching. A full102-channel spectrum cannot be promised for every position or date; the spectral-sampling control requests more real observations rather than filling missing channels.

Temperature, line velocities and orbit models are conditional calculations. A planet's distance or orbit is not automatically determined by clicking a moving feature. Missing, blended, saturated or low-quality observations remain unavailable.

## Deployment

See [DEPLOY.md](DEPLOY.md) for GitHub and Streamlit Community Cloud. Use `app.py` as the entry point and Python3.13. A Dockerfile is also supplied; Docker hosting has not been executed in this environment.

## Sources and acknowledgement

- [NASA/IPAC SPHEREx archive](https://irsa.ipac.caltech.edu/Missions/spherex.html)
- [Official SPHEREx tutorials](https://caltech-ipac.github.io/irsa-tutorials/spherex/)
- [IRSA spectrophotometry documentation](https://irsa.ipac.caltech.edu/onlinehelp/spherex/spherex/sp.html)
- [SIMBAD](https://simbad.harvard.edu/simbad/), [GaiaDR3 through VizieR](https://vizier.cfa.harvard.edu/viz-bin/VizieR?-source=I/355/gaiadr3)
- [CDS Aladin Lite](https://cds-astro.github.io/aladin-lite/)

This publication makes use of data products from the Spectro-Photometer for the History of the Universe, Epoch of Reionization and Ices Explorer (SPHEREx), which is a joint project of the Jet Propulsion Laboratory and the California Institute of Technology, and is funded by the National Aeronautics and Space Administration.
