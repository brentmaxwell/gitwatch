**gitwatch** lets you watch a git repository for changes and run a
custom command per change. I wrote this to keep track of certain
Cyanogenmod features.

Here is an example of how you might use gitwatch to get notified of
changes to btrfs code in linux kernel:

    gitwatch https://github.com/torvalds/linux.git master \
      grep -Pq "'"'^[+-]{3}.*/fs/btrfs/'"'" '&&' echo '$GW_TO' | mail -s 'btrfs' mike

gitwatch runs `grep ...` once for each new change since the last time it
was run. If it's the first time, it will only clone the repo without
running the command. The command is fed the output of `git diff`. Some
other environmental variables are also set (see the comment on top of
the script).

Let's install an hourly cron job on the hour to automate this:

    (crontab -l; echo -n '0 * * * * '; cat <<-EOF | tr '\n' ' '; echo) | crontab

      gitwatch https://github.com/torvalds/linux.git master 
        grep -Pq "'"'^[+-]{3}.*/fs/btrfs/'"'" '&&' echo '$GW_TO' | mail -s 'btrfs' mike
        2>/dev/null

    EOF
