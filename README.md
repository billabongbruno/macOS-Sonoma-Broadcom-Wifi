# macOS-Sonoma-Broadcom-Wifi
A collection of instructions and links to make Broadcom Wifi work natively again under macOS 14 (Sonoma Beta at the time of writing), until such time as proper support is added.
This process involves disabling SIP, so be warned.
Below is a description of what to do.

<br/>

<br/>

**STEP 1:**
Disable Gatekeeper

Under Terminal run, the following:

```
sudo spctl --master-disable
```
<br/>

<br/>

**STEP 2:**
Extract and add these Kexts to your EFI folder:

From source:
[IOSkywalk.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/e21efa975c0cf228cb36e81a974bc6b4c27c7807/payloads/Kexts/Wifi/IOSkywalkFamily-v1.0.0.zip)
and
[IO80211FamilyLegacy.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/e21efa975c0cf228cb36e81a974bc6b4c27c7807/payloads/Kexts/Wifi/IO80211FamilyLegacy-v1.0.0.zip)

From this repo (with the normal EFI structure, if you prefer):
[Here](https://github.com/billabongbruno/macOS-Sonoma-Broadcom-Wifi/releases/download/v14.b2.1.0/Sonoma_BCM94360NG_Wifi_Pack.zip)

<br/>

<br/>

**STEP 3:**
Edit your config.plist so that it reflects these changes (from whatever your values are):

3.1 - Add the new kexts to your existing ones (Kernel - Add)
```
                <dict>
                    <key>Arch</key>
                    <string>Any</string>
                    <key>BundlePath</key>
                    <string>IOSkywalkFamily.kext</string>
                    <key>Comment</key>
                    <string>V1.0</string>
                    <key>Enabled</key>
                    <true/>
                    <key>ExecutablePath</key>
                    <string>Contents/MacOS/IOSkywalkFamily</string>
                    <key>MaxKernel</key>
                    <string></string>
                    <key>MinKernel</key>
                    <string>23.0.0</string>
                    <key>PlistPath</key>
                    <string>Contents/Info.plist</string>
                </dict>
                <dict>
                    <key>Arch</key>
                    <string>Any</string>
                    <key>BundlePath</key>
                    <string>IO80211FamilyLegacy.kext</string>
                    <key>Comment</key>
                    <string>V1200.12.2b1</string>
                    <key>Enabled</key>
                    <true/>
                    <key>ExecutablePath</key>
                    <string>Contents/MacOS/IO80211FamilyLegacy</string>
                    <key>MaxKernel</key>
                    <string></string>
                    <key>MinKernel</key>
                    <string>23.0.0</string>
                    <key>PlistPath</key>
                    <string>Contents/Info.plist</string>
                </dict>
                <dict>
                    <key>Arch</key>
                    <string>Any</string>
                    <key>BundlePath</key>
                    <string>IO80211FamilyLegacy.kext/Contents/PlugIns/AirPortBrcmNIC.kext</string>
                    <key>Comment</key>
                    <string>V1400.1.1</string>
                    <key>Enabled</key>
                    <true/>
                    <key>ExecutablePath</key>
                    <string>Contents/MacOS/AirPortBrcmNIC</string>
                    <key>MaxKernel</key>
                    <string></string>
                    <key>MinKernel</key>
                    <string>23.0.0</string>
                    <key>PlistPath</key>
                    <string>Contents/Info.plist</string>
                </dict>
```

3.2 - Allow for IOSkywalk kext downgrade by excluding it (Kernel - Block)

```
                <dict>
                    <key>Arch</key>
                    <string>Any</string>
                    <key>Comment</key>
                    <string>Allow IOSkywalk Downgrade</string>
                    <key>Enabled</key>
                    <true/>
                    <key>Identifier</key>
                    <string>com.apple.iokit.IOSkywalkFamily</string>
                    <key>MaxKernel</key>
                    <string></string>
                    <key>MinKernel</key>
                    <string>23.0.0</string>
                    <key>Strategy</key>
                    <string>Exclude</string>
                </dict>
```

3.3 - Change SecureBootModel to Disabled (Entries - Security)

```
                <key>SecureBootModel</key>
                <string>Disabled</string>
```

3.4 - Add the AMFI exclusion to your boot-args (NVRAM - Add - 7C436110-AB2A-4BBB-A880-FE41995C9F82)

```
            <dict>
                    <key>boot-args</key>
                    <string>amfi=0x80</string>
            </dict>
```

3.5 - Set your SIP to Disabled (NVRAM - Add - 7C436110-AB2A-4BBB-A880-FE41995C9F82)

```
                    <key>csr-active-config</key>
                    <data>AwgAAA==</data>
```

<br/>

<br/>

**STEP 4:**
Download and install Kernel Debug Kit:

From source:
[Kernel Debug Kit](https://github.com/dortania/KdkSupportPkg/releases/download/23A5301h/Kernel_Debug_Kit_14.0_build_23A5301h.dmg)

<br/>

<br/>

**STEP 5:**
Download and run OCLP and install Root Patch for Modern Wifi (Needed after every update):

From source:
[OCLP 0.6.8](https://github.com/dortania/OpenCore-Legacy-Patcher/releases/download/0.6.8/OpenCore-Patcher-GUI.app.zip)

<br/>

<br/>

All credits go to the [OCLP team](https://github.com/dortania/OpenCore-Legacy-Patcher/), I merely put everything together for ease of use (for me, at least).
