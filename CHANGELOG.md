## 2017-02-15 v0.2.1
- Add exception handling to custom cleaning functions (`CleanZipcodeUS`, `CleanAnnualRevenue`)
- A temporary error in the Eloqua Bulk API caused some fields to not be exported in the standard pull
- This resulted in that set of records erroring out and temporarily getting stuck in the processing queue
- The rest of the DWM is robust enough to handle that kind of error; these custom functions were not 
- Change hardcoded dict config values in Elouqa Contacts DWM config for derive to a list

## 2016-12-13 v0.2.0a
- Added logging on import syncs to record URI for successful syncs - to check for evidence if the sync actions are not working properly

## 2016-12-06 v0.2.0
- Added handling to account for dev/prod environments (didn't have an easy way, outside of manual adjustments, to test in DEV without posting data to PROD)
- Upgraded `pyqm=v0.0.6`
  + Implemented batch tracking on records coming through `Eloqua_Contacts_GetDWM.py`
  + Additional queue stats now served up to Prometheus Pushgateway (age of oldest in queue, max attempts)
  + Use `transfer=True` to ensure uniqueness is preserved across queues, hopefully to avoid duplicates being imported to Eloqua
    - This has been a potential problem because duplicates are completely dropped from any import
- Staggered jobs:
  + `Eloqua_Contacts_GetDWM.py` and `Eloqua_Contacts_PostDWM.py` now run on HH:00/HH:30 and HH:15/HH:45, respectively
  + `Eloqua_Contacts_RunDWM.py` and `Eloqua_Contacts_UpdateContactsIndicators.py` run all other minutes, unless a previous instance is still running
  + None of these run if the daily script, `Eloqua_Contacts.Indicators_Refresh.py`, is still running
- Upgraded `pyeloqua==v0.3.2`
  + Because.
- Added pre-export validation to check shared list `DWM - Export Queue` _before_ creating a sync; this saves on our daily sync limit, especially since now trying to export twice an hour.
- Added hourly script `keepalive.sh`; since ITOS/Openshift Enterprise is _technically_ for web apps, if there's no web traffic to the designated URL after a certain period of time the application will idle, shutting down cron jobs and databases. This script pings the app's DNS once an hour to make it think there's traffic, and hence keep the app alive.

## 2016-11-16 v0.1.2 HOTFIX
- Add handling for POSTing back to Eloqua - if records error out on import, add to `indicatorRefreshErroredQueue` or `dwmPOSTErroredQueue`, depending on circumstance

## 2016-11-07 v0.1.1.0
- Additional solution documentation describing role of each script
- Removal of script file `Eloqua_Contacts_DWM.py`; this has been replaces by multiple scripts which leverage queues (as of v0.1.0)
- Addition to queue process: now updates associated records in CDO Contacts.Indicators, which (via data card services) triggers an update to those records
- Addition of daily script which also triggers updates in Contacts.Indicators for records not refreshed in the last 180 days

## 2016-10-07 v0.1.0
- Upgrade pyeloqua v0.2.91 for misc bug fixes
- Implement queue-based processing system (see README)

## 2016-09-29 v0.0.10
- Upgrade to pyeloqua v0.2.7
- Now feeding based on shared list; exports from shared list and uses sync action on import to remove from shared list
- Officially added some console logging to the cron bash
- Officially added actual dwmAll processing time stat to push to Prometheus

## 2016-09-22 v0.0.9
- Upgrade to dwm v0.0.6
- Add console dump log to catch warnings/errors

## 2016-09-20 v0.0.8
- Lower count to 20,000 records at once, until queuing is setup

## 2016-09-20 v0.0.7
- Upgrade to pyeloqua v0.2.6 to fix sync count reporting issue

## 2016-09-20 v0.0.6
- Upgrade to dwm v0.0.5 (hotfix); this loops through derive fields by the config order, not by data order

## 2016-09-19 v0.0.5
- Added cron job
- Small updates to README

## 2016-09-18 v0.0.4
- Added logic to reset "SYSTEM: External Processing Flags"
- Upgraded pyeloqua==0.2.5
- Set logic to pull only 30,000 records at once

## 2016-09-16 v0.0.3
- Fixed requirements, etc, added prometheus_client
- Added config
- added in custom functions file

## 2016-09-16 v0.0.2
- added requirements.txt

## 2016-09-16 v0.0.1
- Initial setup
