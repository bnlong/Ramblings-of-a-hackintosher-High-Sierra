# Examples

These are examples for configs and such. Do **not** copy these directly, these are only examples.

## haswell_config.plist
This config.plist is an example for Haswell systems. This config includes:
* InjectKexts = Yes
* NvidiaWeb
* XHCI port limit patch + USB injection
* A fully setup SMBIOS
* Apple RTC, AppleIntelCPUPM & KernelPM
* A setup GUI
* Audio layout 1 injection
* `-v`, `-xcpm`, `dart=0` & `debug=0x100` boot arguments
* MATS table dropped (this is needed for some Gigabyte motherboards)
* XMP Detection
* FixShutdown
* Disabled SIP

## kaby_lake_config.plist
This config.plist is an example for Kaby lake systems. This config includes:
* InjectsKexts = Yes
* Skylake/Kaby Lake USB properties
* iGPU injection and ig-platform-id
* XHCI port limit patch + USB injection
* Apple RTC, AppleIntelCPUPM & KernelPM
* A fully setup SMBIOS and ROM & MLB
* NvidiaWeb
* Audio layout 1 injection
* `-v`, `-xcpm`, `dart=0` & `debug=0x100` boot arguments
* DMAR table dropped (needed for skylake)
* HDAS to HDEF rename for audio
* FixShutdown
* XMP detection
* System ID injection
* Disabled SIP
