---
layout: page
title: "WordPress Cron"
category: configuration
date: 2019-03-15 13:18:00
order: 5
---

> **Beware of bad advice!** There are a lot of old guides out there that recommend putting something like `*/1 * * * * curl https://example.com/wp/wp-cron.php` in your system cron. _Never do that!_ It is outdated advice and will only do harm to any modern WordPress project.

The WordPress cron fully works out-of-the-box and developers do not have to do anything special about it. To register your own scheduled events, please learn the [WordPress Cron API](https://codex.wordpress.org/Function_Reference/wp_schedule_event) carefully and in general do read the excellent [WordPress Plugin Developer Handbook](https://developer.wordpress.org/plugins/cron/) so you know how to design your WordPress plugin or theme code correctly from the start.

## What is scheduled

Any plugin or theme can schedule events, so the list of events for each site is individual. The easiest way to check it out is using [wp-cli](https://wp-cli.org/):

```
$ wp cron event list
+-------------------------------------------------+---------------------+-----------------------+---------------+
| hook                                            | next_run_gmt        | next_run_relative     | recurrence    |
+-------------------------------------------------+---------------------+-----------------------+---------------+
| check_plugin_updates-profile-builder-pro-update | 2019-03-15 11:16:12 | now                   | 12 hours      |
| wp_privacy_delete_old_export_files              | 2019-03-15 11:18:22 | 1 minute 25 seconds   | 1 hour        |
| expire_user_cron                                | 2019-03-15 11:31:45 | 14 minutes 48 seconds | 1 hour        |
| ao_cachechecker                                 | 2019-03-15 15:21:23 | 4 hours 4 minutes     | 12 hours      |
| wp_update_plugins                               | 2019-03-15 17:16:29 | 5 hours 59 minutes    | 12 hours      |
| wp_update_themes                                | 2019-03-15 17:16:29 | 5 hours 59 minutes    | 12 hours      |
| wp_scheduled_delete                             | 2019-03-15 17:17:09 | 6 hours               | 1 day         |
| delete_expired_transients                       | 2019-03-15 17:17:09 | 6 hours               | 1 day         |
| wp_version_check                                | 2019-03-15 17:18:01 | 6 hours 1 minute      | 12 hours      |
| check_plugin_updates-wppb-bdp-add-on            | 2019-03-15 17:36:46 | 6 hours 19 minutes    | 12 hours      |
| sm_ping_daily                                   | 2019-03-15 18:20:52 | 7 hours 3 minutes     | 1 day         |
| wp_scheduled_auto_draft_delete                  | 2019-03-15 18:50:37 | 7 hours 33 minutes    | 1 day         |
| trigger_live_discussion_notification            | 2019-03-18 07:40:00 | 2 days 20 hours       | Non-repeating |
| trigger_live_discussion_notification            | 2019-04-08 12:45:00 | 3 weeks 3 days        | Non-repeating |
+-------------------------------------------------+---------------------+-----------------------+---------------+
```

Note the column **next_run_relative**. If there are many items marked **now** and they stay as such, it is a sign that the cron is not running correctly. If a developer does a web search on "WordPress" and "cron" they often find advice about running _curl_ or _wget_ from the system cron. Such advice is plain *outdated* and should be completely discarded, as it will not fix the issue at all. Never do that. If there is a problem with a scheduled event not running, the code needs to be debugged and fixed.

## Testing cron jobs

The great wp-cli has a built-in function to test cron. It can look like this:
```
$ wp cron test
Error: The DISABLE_WP_CRON constant is set to true. WP-Cron spawning is disabled.
```

Alternatively you can try running all due events:
```
$ wp cron event run --due-now
Executed the cron event 'check_plugin_updates-profile-builder-pro-update' in 0.175s.
Success: Executed a total of 1 cron event.
```

One may also trigger an individual event like this:
```
$ wp cron event run check_plugin_updates-profile-builder-pro-update
Executed the cron event 'check_plugin_updates-profile-builder-pro-update' in 0.17s.
Success: Executed a total of 1 cron event.
```

If the function executes and there are no errors immediately visible, or in the `/data/log/php-error.log`, then the code in that function probably works.