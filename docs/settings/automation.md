# External noGUI Scheduling

💲 [Sync Schedule](scheduling.md) is the standard Pro workflow; see [Free vs Pro](../getting-started/free-vs-pro.md). Non-Pro users can use an operating-system scheduler to run IPTVBoss noGUI synchronization. The examples below use the confirmed default commands.

!!! warning
    IPTVBoss must be closed before a noGUI run starts. Do not run the desktop application and noGUI synchronization against the same database at the same time.

## Windows Task Scheduler

1. Close IPTVBoss.
2. Open **Task Scheduler** in Windows.
3. Create a new task named `Boss Sync`.
4. Under **Triggers**, add the times when synchronization should run.
5. Under **Actions**, select **New**.
6. Set **Start a program** as the action.
7. Set **Program/script** to:

   ```text
   C:\Windows\System32\cmd.exe
   ```

8. Set **Add arguments** to:

   ```text
   /C IPTVBoss -noGUI
   ```

9. Under **Conditions**, enable **Wake the computer to run this task** if the computer may be asleep at the scheduled time.
10. Under **Settings**, enable **Allow task to be run on demand**.
11. Enable the option to stop the task if it runs longer than **4 hours**.
12. Enable the option to force the task to close if it does not end when requested.
13. Save the task.

## Test the task

1. Make sure the normal IPTVBoss desktop application is closed.
2. In Task Scheduler, select **Boss Sync**.
3. Choose **Run**.
4. Check Task Manager after a short delay. IPTVBoss should appear while the noGUI process is running.
5. Review the IPTVBoss logs and confirm that source and EPG synchronization completed.

If the task cannot find IPTVBoss, use the full path to the installed executable in the action or configure the task's **Start in** directory to the IPTVBoss installation directory.

## Linux cron

1. Close IPTVBoss.
2. Open the user crontab:

   ```bash
   crontab -e
   ```

3. Add an entry using the desired minute and hour. For a 03:00 run:

   ```cron
   0 3 * * * iptvboss -nogui
   ```

4. Replace `iptvboss` with the full executable path when it is not on `PATH`.
5. Save the crontab.
6. Test the command manually, then review the IPTVBoss logs after the first scheduled run.

## macOS cron

1. Close IPTVBoss.
2. Open the user crontab:

   ```bash
   crontab -e
   ```

3. Add an entry using the desired minute and hour. For a 03:00 run with the default application path:

   ```cron
   0 3 * * * /Applications/IPTVBoss.app/Contents/MacOS/IPTVBoss -nogui
   ```

4. Replace the application path when IPTVBoss is installed elsewhere.
5. Save the crontab.
6. Test the command manually, then review the IPTVBoss logs after the first scheduled run.

The native scheduler may manage these entries automatically when Pro access is enabled. Avoid manually adding a second cron entry for the same schedule.
