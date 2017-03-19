# Arnold

Test enviornment smoke tests and debugging tools in bash

If we have an environment called terminator containing the services time-travel, john-connors and sarah-connors defined in our arnold config we can make it really easy to check the environment and services out from our local machine.

```
arnold is terminator up
arnold is terminator time-travel up
arnold has terminator time-travel logged-errors
arnold has terminator sarah-connors died
arnold can terminator john-connors recieve-messages
arnold can terminator john-connors send-messages
arnold do terminator service-reboot
arnold do terminator deploy
```

We can run commands together as well:

```
arnold do terminator reboot and do terminator deploy and is terminator up
```

To configure an environment we can use these commands:

```
arnold env create terminator arnold@10.1.88.12:22 ~/.ssh/arnold-terminator.pub
arnold env terminator add state up "ssh -T arnold@10.1.88.12:22 || false" "terminator is up" "terminator is down"
arnold env terminator add state broker-running 'ps -ef | grep Mail | head -n 1 | sed "s/ \{1,\}/,/g" | cut -f 9 -d "," | grep "Mail" || false'
arnold env terminator add action reboot "docker-compose down time-travel sarah-connors john-connors && docker-compose up time-travel sarah-connors john-connors"
arnold env terminator create service time-travel
arnold env terminator service time-travel add state logged-errors "cat /data/logs/time-travel.log | grep ERROR && false" "No errors on time-travel" "time-travel has errors"
arnold env terminator service time-travel add action rotate-logs "mv /data/logs/time-travel.log /data/logs/archive/${time}.time-travel.log && touch /data/logs/time-travel.log" "Logs for time-travel rotated" "Could not rotate the time-travel logs"
```

If we want to rename or change the credentials of thins we can use these commands:

```
arnold env update terminator me@10.1.88.12:22 ~/.ssh/my_key.pub
arnold env terminator service time-travel update name magic
```

Sometimes these commands get a bit long so we can alias things:

```
arnold alias "has terminator T1000 got-boots" "got boots"
arnold alias "do terminator down and do terminator deploy and do terminator up and is terminator up" "reload terminator"
```

Arnold uses your pwd to work out which arnold config to use. Defaulting to a global config at ~/.arnold/config.properties. You can also set CURRENT_ARNOLD manually and run arnold prefer CURRENT_ARNOLD.

Obviously tab completion works. And all this stuff is stored in a flat file so we can check it in and share it easily.
