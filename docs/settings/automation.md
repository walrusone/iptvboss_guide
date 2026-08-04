# Automatic Synchronization on Windows

Non-Pro users can use Windows Task Scheduler to run the IPTVBoss noGUI synchronization task on a schedule.

## Create the task

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

!!! warning
    Do not run the desktop application and noGUI synchronization against the same database at the same time. Concurrent writers can cause conflicts or prevent a scheduled run from starting.

If the task cannot find IPTVBoss, use the full path to the installed executable in the action or configure the task's **Start in** directory to the IPTVBoss installation directory.
