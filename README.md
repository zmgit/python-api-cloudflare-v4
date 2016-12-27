# *pycloudflare-v4*
A ***HUMAN READABLE*** and ***SCRIPTING EASY*** Python wrapper for CloudFlare API v4

## *Current Version:*
-  0.7.0

## *Covered Methods:*

- Zone:
    - [x] List zones (https://api.cloudflare.com/#zone-list-zones)
    - [x] Purge all files (https://api.cloudflare.com/#zone-purge-all-files)
-  Zone Settings:
    - [x] Get all Zone Settings (https://api.cloudflare.com/#zone-settings-properties)
    - [x] Change Always Online setting (https://api.cloudflare.com/#zone-settings-change-always-online-setting)
    - [x] Change Automatic HTTPS Rewrites setting (https://api.cloudflare.com/#zone-settings-change-automatic-https-rewrites-setting)
    - [x] Change Browser Cache TTL setting (https://api.cloudflare.com/#zone-settings-change-browser-cache-ttl-setting)
    - [x] Change Browser Check setting (https://api.cloudflare.com/#zone-settings-change-browser-check-setting)
    - [x] Change Cache Level setting (https://api.cloudflare.com/#zone-settings-change-cache-level-setting)
    - [x] Change Challenge TTL setting (https://api.cloudflare.com/#zone-settings-change-challenge-ttl-setting)
    - [x] Change Development Mode setting (https://api.cloudflare.com/#zone-settings-change-development-mode-setting)
    - [x] Change Email Obfuscation setting (https://api.cloudflare.com/#zone-settings-change-email-obfuscation-setting)
    - [x] Change Enable Error Pages On setting (https://api.cloudflare.com/#zone-settings-change-enable-error-pages-on-setting)
    - [ ] Change Enable Query String Sort setting (https://api.cloudflare.com/#zone-settings-change-enable-query-string-sort-setting)
- DNS Records for a Zone:
    - List DNS records(https://api.cloudflare.com/#dns-records-for-a-zone-list-dns-records)
    - Create DNS record(https://api.cloudflare.com/#dns-records-for-a-zone-create-dns-record)
    - Update DNS record(https://api.cloudflare.com/#dns-records-for-a-zone-update-dns-record)

## *Installation*

```bash
$ git clone https://github.com/zmgit/pycloudflare-v4
$ cd pycloudflare-v4
$ pip install -r requirements.txt
$ sudo ./setup.py install
```

## *Getting Started*

A very simple listing of zones within your account; including the IPv6 status of the zone.

```python
from pycloudflare_v4 import api
 
def main():
 
    cfapi = api.CloudFlare("email", "api_token")
 
    # Get all zones
    zones = cfapi.get_zones()
    for z_name, z_details in zones.iteritems():
        zone_name = z_details['name']
        zone_id = z_details['id']
        zone_status = z_details['status']
        print zone_id, zone_name, zone_status
 
    # Purge cache for all zones
    for k, v in zones.iteritems():
        try:
            purged = cfapi.purge_everything(v['id'])
        except BaseException as e:
            print str(e)
        finally:
            if purged['success']:
                print k, "[ SUCCESS ]"
            else:
                print purged['errors'][0]['message']
 
    # Purge cache for example.com zone
        print cfapi.purge_everything(zones['example.com']['id'])
 
    # Get all DNS records
    for k, v in zones.iteritems():
        records = cfapi.dns_records(v['id'])
        print k
        for i in records:
            print i['name'], i['type'], i['id']
 
    # Create DNS record
    print cfapi.dns_records_create(zone_id=zone_id,
                                   record_type="A",
                                   record_name="test",
                                   record_content="1.1.1.1")
 
    # Update DNS record
    for k, v in zones.iteritems():
        records = cfapi.dns_records(v['id'])
        print k
        for i in records:
            print cfapi.dns_records_update(zone_id=zone_id,
                                           record_id=i['id'],
                                           proxied='true',
                                           ttl=1)
 
    # Get zone settings
    for z_name, z_details in zones.iteritems():
        zone_name = z_details['name']
        zone_id = z_details['id']
        zone_status = z_details['status']
        print zone_name, ": ", zone_status
        print cfapi.get_all_zone_settings(zone_id)
 
    # Set example.com zone settings
    print cfapi.change_always_online_setting(zones['example.com']['id'], always_online="default")
    print cfapi.change_automatic_https_rewrites_setting(zones['example.com']['id'], automatic_https_rewrites="default")
    print cfapi.change_browser_cache_ttl_setting(zones['example.com']['id'], browser_cache_ttl="default")
    print cfapi.change_browser_check_setting(zones['example.com']['id'], browser_check="default")
 
if __name__ == '__main__':
    main()
```