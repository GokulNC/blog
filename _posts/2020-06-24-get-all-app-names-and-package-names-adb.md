---
layout: post
title:  "How to get all Android app names and package names using ADB?"
date:   2020-06-24 17:01:11 +0530
categories: android
---

## Pre-requisites

- [Install `aapt` binary on Android](/blog/find-app-name-adb)
- Install Python and pip
- `pip install tqdm`

## Script to list all app names

Filename:
> list_all_apps_android.py

Code:
```python
import sys
from subprocess import Popen, PIPE, STDOUT
from tqdm import tqdm

AAPT_PATH = '/data/local/tmp/aapt-arm-pie'

def run_command(command):
	out = Popen(command.split(), shell=True, stdout=PIPE)
	stdout, stderr = out.communicate()
	if stderr:
		stderr = stderr.decode("utf-8").strip()
		print(stderr)
	return stdout.decode("utf-8").strip().replace('\r\n', '\n')

def get_app_name_from_apk(apk_path):
	command = 'adb shell %s dump badging %s' % (AAPT_PATH, apk_path)
	output = run_command(command)
	for line in output.split('\n'):
		if line.startswith('application-label:'):
			return line.replace('application-label:', '').replace('\'', '')
	
	return '<FAILED>'

def get_all_package_names(sys_only=False, third_party_only=False):
	command = 'adb shell pm list packages -f'
	if sys_only:
		command += ' -s'
	elif third_party_only:
		command += ' -3'
	
	pkg2name = {}
	output = run_command(command)
	for line in tqdm(output.split('\n')):
		line = line.replace('package:', '')
		delim_index = line.rfind('=')
		pkg_name = line[delim_index+1:]
		apk_path = line[:delim_index]
		app_name = get_app_name_from_apk(apk_path)
		pkg2name[pkg_name] = app_name
	
	return pkg2name
	
	return

if __name__ == '__main__':
	pkg2name = get_all_package_names()
	
	# Write output to CSV
	csv_str = 'PACKAGE_NAME,APP_NAME\n'
	for pkg_name in sorted(list(pkg2name.keys())):
		csv_str += '%s,%s\n' % (pkg_name,pkg2name[pkg_name])
	
	output_file = sys.argv[1]
	with open(output_file, 'w', encoding='utf-8') as f:
		f.write(csv_str)
```

To run:
```bash
python list_all_apps_android.py apps.csv
```

This will save the list of all app and package names to the `output_file` named `apps.csv`

## Sample output

```csv
PACKAGE_NAME,APP_NAME
com.android.bluetoothmidiservice,Bluetooth MIDI Service
com.android.bookmarkprovider,Bookmark Provider
com.android.browser,Browser Services
com.android.calendar,Calendar
com.android.calllogbackup,Call Log Backup/Restore
com.android.camera,Camera
com.android.captiveportallogin,CaptivePortalLogin
com.android.carrierconfig,<FAILED>
com.android.carrierconfig.overlay.common,<FAILED>
com.android.carrierdefaultapp,CarrierDefaultApp
com.android.cellbroadcastreceiver,Emergency alerts
com.android.cellbroadcastreceiver.overlay.common,<FAILED>
com.android.certinstaller,Certificate Installer
com.android.chrome,Chrome
com.android.companiondevicemanager,Companion Device Manager
com.android.contacts,Contacts and dialer
com.android.deskclock,Clock
com.android.dreams.basic,Basic Daydreams
com.android.dreams.phototable,Photo Screensavers
com.android.dynsystem,Dynamic System Updates
com.android.egg,Android Q Easter Egg
com.android.emergency,Emergency information
com.android.externalstorage,External Storage
com.android.fileexplorer,<FAILED>
com.android.hotspot2,OsuLogin
com.android.hotwordenrollment.okgoogle,OK Google enrollment
com.android.hotwordenrollment.xgoogle,X Google enrollment
com.android.htmlviewer,HTML Viewer
com.android.incallui,Phone
com.android.inputdevices,Input Devices
com.android.internal.display.cutout.emulation.corner,Corner cutout
com.android.internal.display.cutout.emulation.double,Double cutout
com.android.internal.display.cutout.emulation.tall,Tall cutout
com.android.internal.systemui.navbar.gestural,Gestural Navigation Bar
com.android.internal.systemui.navbar.gestural_extra_wide_back,Gestural Navigation Bar
com.android.internal.systemui.navbar.gestural_narrow_back,Gestural Navigation Bar
com.android.internal.systemui.navbar.gestural_wide_back,Gestural Navigation Bar
com.android.internal.systemui.navbar.threebutton,3 Button Navigation Bar
com.android.internal.systemui.navbar.twobutton,2 Button Navigation Bar
com.android.keychain,Key Chain
```
