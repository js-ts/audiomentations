# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Unreleased

## [0.27.0] - 2022-09-13

### Changed

* Speed up `Limiter` by ~8x
* Fix/improve some docstrings and type hints
* Change default values in `Trim` and `ApplyImpulseResponse` according to the warnings that were added in v0.23.0
* Emit a FutureWarning when `noise_rms` in `AddShortNoises` is not specified - the 
 default value will change from "relative" to "relative_to_whole_input" in a future version. 

## [0.26.0] - 2022-08-19

### Added

* Add new transform `Lambda`. Thanks to Thanatoz-1.
* Add new transform `Limiter`. Thanks to pzelasko.

### Fixed

* Fix incorrect type hints in `RoomSimulator`
* Make `Shift` robust to different sample rate inputs when parameters are frozen

## [0.25.1] - 2022-06-15

### Fixed

* Fix a bug where `RoomSimulator` would treat an x value as if it was y, and vice versa

## [0.25.0] - 2022-05-30

### Added

* Add `AirAbsorption` transform
* Add mp4 to the list of recognized audio filename extensions

### Changed

* Guard against invalid params in `TimeMask`
* Emit `FutureWarning` instead of `UserWarning` in `Trim` and `ApplyImpulseResponse`
* Allow specifying a file path, a folder path, a list of files or a list of folders to
  `ApplyImpulseResponse`, `AddBackgroundNoise` and `AddShortNoises`. Previously only a path to a folder was allowed.

### Fixed

* Fix a bug with `noise_transform` in `AddBackgroundNoise` where some
  SNR calculations were done before the `noise_transform` was applied. This has sometimes
  led to incorrect SNR in the output. **This changes the behavior** of
  `AddBackgroundNoise` (when noise_transform is used).

### Removed

* Remove support for Python 3.6, as it is past its end of life already. RIP.

## [0.24.0] - 2022-03-18

### Added

* Add `SevenBandParametricEQ` transform
* Add optional `noise_transform` in `AddShortNoises`
* Add .aac and .aif to the list of recognized audio filename endings

### Changed

* Show warning if `top_db` and/or `p` in `Trim` are not specified because their default
  values will change in a future version

### Fixed

* Fix filter instability bug related to center freq above nyquist freq in `LowShelfFilter` and `HighShelfFilter`

## [0.23.0] - 2022-03-07

### Added

* Add `Padding` transform
* Add `RoomSimulator` transform for simulating shoebox rooms using `pyroomacoustics`
* Add parameter `signal_gain_in_db_during_noise` in `AddShortNoises`

### Changed

* Not specifying a value for `leave_length_unchanged` in `AddImpulseResponse` now emits
  a warning, as the default value will change from `False` to `True` in a future version.

### Removed

* Remove the deprecated `AddImpulseResponse` alias. Use `ApplyImpulseResponse` instead.
* Remove support for the legacy parameters `min_SNR` and `max_SNR` in `AddGaussianSNR`
* Remove useless default path value in `AddBackgroundNoise`, `AddShortNoises` and `ApplyImpulseResponse`

## [0.22.0] - 2022-02-18

### Added

* Implement `GainTransition`
* Add support for librosa 0.9
* Add support for stereo audio in `Mp3Compression`, `Resample` and `Trim`
* Add `"relative_to_whole_input"` option for `noise_rms` parameter in `AddShortNoises`
* Add optional `noise_transform` in `AddBackgroundNoise`

### Changed

* Improve speed of `PitchShift` by 6-18% when the input audio is stereo

### Removed

* Remove support for librosa<=0.7.2

## [0.21.0] - 2022-02-10

### Added

* Add support for multichannel audio in `ApplyImpulseResponse`, `BandPassFilter`, `HighPassFilter` and `LowPassFilter`
* Add `BandStopFilter` (similar to FrequencyMask, but with overhauled defaults and parameter randomization behavior), `PeakingFilter`, `LowShelfFilter` and `HighShelfFilter`
* Add parameter `add_all_noises_with_same_level` in `AddShortNoises`

### Changed

* Change `BandPassFilter`, `LowPassFilter`, `HighPassFilter`, to use scipy's butterworth
  filters instead of pydub. Now they have parametrized roll-off. Filters are now steeper
  than before by default - set `min_rolloff=6, max_rolloff=6` to get the old behavior.
  They also support zero-phase filtering now. And they're at least ~25x times faster than before!

### Removed

* Remove optional `wavio` dependency for audio loading

## [0.20.0] - 2021-11-18

### Added

* Implement `OneOf` and `SomeOf` for applying one of or some of many transforms. Transforms are randomly
  chosen every call. Inspired by augly. Thanks to Cangonin and iver56.
* Add a new argument `apply_to_children` (bool) in `randomize_parameters`,
  `freeze_parameters` and `unfreeze_parameters` in `Compose` and `SpecCompose`.

### Changed

* Insert three new parameters in `AddBackgroundNoise`: `noise_rms` (defaults to "relative", which is
  the old behavior), `min_absolute_rms_in_db` and `max_absolute_rms_in_db`. This **may be a breaking
  change** if you used `AddBackgroundNoise` with positional arguments in earlier versions of audiomentations!
  Please use keyword arguments to be on the safe side - it should be backwards compatible then.

### Fixed

* Remove global `pydub` import which was accidentally introduced in v0.18.0. `pydub` is
  considered an optional dependency and is imported only on demand now.

## [0.19.0] - 2021-10-18

### Added

* Implement `TanhDistortion`. Thanks to atamazian and iver56.
* Add a `noise_rms` parameter to `AddShortNoises`. It defaults to `relative`, which
  is the old behavior. `absolute` allows for adding loud noises to parts that are
  relatively silent in the input.

## [0.18.0] - 2021-08-05

### Added

* Implement `BandPassFilter`, `HighPassFilter`, `LowPassFilter` and `Reverse`. Thanks to atamazian.

## [0.17.0] - 2021-06-25

### Added

* Add a `fade` option in `Shift` for eliminating unwanted clicks
* Add support for 32-bit int wav loading with scipy>=1.6
* Add support for float64 wav files. However, the use of this format is discouraged,
  since float32 is more than enough for audio in most cases.
* Implement `Clip`. Thanks to atamazian.
* Add some parameter sanity checks in `AddGaussianNoise`
* Officially support librosa 0.8.1

### Changed

* Rename `AddImpulseResponse` to `ApplyImpulseResponse`. The former will still work for
  now, but give a warning.
* When looking for audio files in `AddImpulseResponse`, `AddBackgroundNoise`
  and `AddShortNoises`, follow symlinks by default.
* When using the new parameters `min_snr_in_db` and `max_snr_in_db` in `AddGaussianSNR`,
  SNRs will be picked uniformly in _the decibel scale_ instead of in the linear amplitude
  ratio scale. The new behavior aligns more with human hearing, which is not linear.

### Fixed

* Avoid division by zero in `AddImpulseResponse` when input is digital silence (all zeros)
* Fix inverse SNR characteristics in `AddGaussianSNR`. It will continue working as before
  unless you switch to the new parameters `min_snr_in_db` and `max_snr_in_db`. If you
  use the old parameters, you'll get a warning.

## [0.16.0] - 2021-02-11

### Added

* Implement `SpecCompose` for applying a pipeline of spectrogram transforms. Thanks to omerferhatt.

### Fixed

* Fix a bug in `SpecChannelShuffle` where it did not support more than 3 audio channels. Thanks to omerferhatt.
* Limit scipy version range to >=1.0,<1.6 to avoid issues with loading 24-bit wav files.
  Support for scipy>=1.6 will be added later.

## [0.15.0] - 2020-12-10

### Added

* Add an option `leave_length_unchanged` to `AddImpulseResponse`

### Fixed

* Fix picklability of instances of `AddImpulseResponse`, `AddBackgroundNoise`
  and `AddShortNoises`

## [0.14.0] - 2020-12-06

### Added

* Implement `LoudnessNormalization`
* Implement `randomize_parameters` in `Compose`. Thanks to SolomidHero.
* Add multichannel support to `AddGaussianNoise`, `AddGaussianSNR`, `ClippingDistortion`,
  `FrequencyMask`, `PitchShift`, `Shift`, `TimeMask` and `TimeStretch`

## [0.13.0] - 2020-11-10

### Added

* Lay the foundation for spectrogram transforms. Implement `SpecChannelShuffle` and
  `SpecFrequencyMask`.
* Configurable LRU cache for transforms that use external sound files. Thanks to alumae.
* Officially add multichannel support to `Normalize`

### Changed

* Show a warning if a waveform had to be resampled after loading it. This is because resampling
  is slow. Ideally, files on disk should already have the desired sample rate.

### Fixed

* Correctly find audio files with upper case filename extensions.
* Fix a bug where AddBackgroundNoise crashed when trying to add digital silence to an input. Thanks to juheeuu.

## [0.12.1] - 2020-09-28

### Changed

* Speed up `AddBackgroundNoise`, `AddShortNoises` and `AddImpulseResponse` by loading wav files with scipy or wavio instead of librosa.

## [0.12.0] - 2020-09-23

### Added

* Implement `Mp3Compression`
* Officially support multichannel audio in `Gain` and `PolarityInversion`
* Add m4a and opus to the list of recognized audio filename extensions

### Changed

* Expand range of supported `librosa` versions

### Removed

* Python <= 3.5 is no longer officially supported, since [Python 3.5 has reached end-of-life](https://devguide.python.org/#status-of-python-branches)
* Breaking change: Internal util functions are no longer exposed directly. If you were doing
  e.g. `from audiomentations import calculate_rms`, now you have to do
  `from audiomentations.core.utils import calculate_rms`

## [0.11.0] - 2020-08-27

### Added

* Implement `Gain` and `PolarityInversion`. Thanks to Spijkervet for the inspiration.

## [0.10.1] - 2020-07-27

### Changed

* Improve the performance of `AddBackgroundNoise` and `AddShortNoises` by optimizing the implementation of `calculate_rms`.

### Fixed

* Improve compatibility of output files written by the demo script. Thanks to xwJohn.
* Fix division by zero bug in `Normalize`. Thanks to ZFTurbo.

## [0.10.0] - 2020-05-05

### Added

* `AddImpulseResponse`, `AddBackgroundNoise` and `AddShortNoises` now support aiff files in addition to flac, mp3, ogg and wav

### Changed

* Breaking change: `AddImpulseResponse`, `AddBackgroundNoise` and `AddShortNoises` now include subfolders when searching for files. This is useful when your sound files are organized in subfolders.

### Fixed

* Fix filter instability bug in `FrequencyMask`. Thanks to kvilouras.

## [0.9.0] - 2020-02-20

### Added

* Remember randomized/chosen effect parameters. This allows for freezing the parameters and applying the same effect to multiple sounds. Use transform.freeze_parameters() and transform.unfreeze_parameters() for this.
* Implement transform.serialize_parameters(). Useful for when you want to store metadata on how a sound was perturbed.
* Add a rollover parameter to `Shift`. This allows for introducing silence instead of a wrapped part of the sound.
* Add support for flac in `AddImpulseResponse`
* Implement `AddBackgroundNoise` transform. Useful for when you want to add background noise to all of your sound. You need to give it a folder of background noises to choose from.
* Implement `AddShortNoises`. Useful for when you want to add (bursts of) short noise sounds to your input audio.

### Changed

* Disregard non-audio files when looking for impulse response files
* Switch to a faster convolve implementation. This makes `AddImpulseResponse` significantly faster.
* Expand supported range of librosa versions

### Fixed

* Fix a bug in `ClippingDistortion` where the min_percentile_threshold was not respected as expected.
* Improve handling of empty input

## [0.8.0] - 2020-01-28

### Added

* Add shuffle parameter in `Composer`
* Add `Resample` transformation
* Add `ClippingDistortion` transformation
* Add `fade` parameter to `TimeMask`

Thanks to askskro

## [0.7.0] - 2020-01-14

### Added

* `AddGaussianSNR`
* `AddImpulseResponse`
* `FrequencyMask`
* `TimeMask`
* `Trim`

Thanks to karpnv

## [0.6.0] - 2019-05-27

### Added

* Implement peak normalization

## [0.5.0] - 2019-02-23

### Added

* Implement `Shift` transform

### Changed

* Ensure p is within bounds

## [0.4.0] - 2019-02-19

### Added

* Implement `PitchShift` transform

### Fixed

* Fix output dtype of `AddGaussianNoise`

## [0.3.0] - 2019-02-19

### Added

* Implement `leave_length_unchanged` in `TimeStretch`

## [0.2.0] - 2019-02-18

### Added

* Add `TimeStretch` transform
* Parametrize `AddGaussianNoise`

## [0.1.0] - 2019-02-15

### Added

* Initial release. Includes only one transform: `AddGaussianNoise`

[Unreleased]: https://github.com/iver56/audiomentations/compare/v0.27.0...HEAD
[0.27.0]: https://github.com/iver56/audiomentations/compare/v0.26.0...v0.27.0
[0.26.0]: https://github.com/iver56/audiomentations/compare/v0.25.1...v0.26.0
[0.25.1]: https://github.com/iver56/audiomentations/compare/v0.25.0...v0.25.1
[0.25.0]: https://github.com/iver56/audiomentations/compare/v0.24.0...v0.25.0
[0.24.0]: https://github.com/iver56/audiomentations/compare/v0.23.0...v0.24.0
[0.23.0]: https://github.com/iver56/audiomentations/compare/v0.22.0...v0.23.0
[0.22.0]: https://github.com/iver56/audiomentations/compare/v0.21.0...v0.22.0
[0.21.0]: https://github.com/iver56/audiomentations/compare/v0.20.0...v0.21.0
[0.20.0]: https://github.com/iver56/audiomentations/compare/v0.19.0...v0.20.0
[0.19.0]: https://github.com/iver56/audiomentations/compare/v0.18.0...v0.19.0
[0.18.0]: https://github.com/iver56/audiomentations/compare/v0.17.0...v0.18.0
[0.17.0]: https://github.com/iver56/audiomentations/compare/v0.16.0...v0.17.0
[0.16.0]: https://github.com/iver56/audiomentations/compare/v0.15.0...v0.16.0
[0.15.0]: https://github.com/iver56/audiomentations/compare/v0.14.0...v0.15.0
[0.14.0]: https://github.com/iver56/audiomentations/compare/v0.13.0...v0.14.0
[0.13.0]: https://github.com/iver56/audiomentations/compare/v0.12.1...v0.13.0
[0.12.1]: https://github.com/iver56/audiomentations/compare/v0.12.0...v0.12.1
[0.12.0]: https://github.com/iver56/audiomentations/compare/v0.11.0...v0.12.0
[0.11.0]: https://github.com/iver56/audiomentations/compare/v0.10.1...v0.11.0
[0.10.1]: https://github.com/iver56/audiomentations/compare/v0.10.0...v0.10.1
[0.10.0]: https://github.com/iver56/audiomentations/compare/v0.9.0...v0.10.0
[0.9.0]: https://github.com/iver56/audiomentations/compare/v0.8.0...v0.9.0
[0.8.0]: https://github.com/iver56/audiomentations/compare/v0.7.0...v0.8.0
[0.7.0]: https://github.com/iver56/audiomentations/compare/v0.6.0...v0.7.0
[0.6.0]: https://github.com/iver56/audiomentations/compare/v0.5.0...v0.6.0
[0.5.0]: https://github.com/iver56/audiomentations/compare/v0.4.0...v0.5.0
[0.4.0]: https://github.com/iver56/audiomentations/compare/v0.3.0...v0.4.0
[0.3.0]: https://github.com/iver56/audiomentations/compare/v0.2.0...v0.3.0
[0.2.0]: https://github.com/iver56/audiomentations/compare/v0.1.0...v0.2.0
[0.1.0]: https://github.com/iver56/audiomentations/releases/tag/v0.1.0
