---
title: "Setup"
teaching: 15
exercises: 0
questions:
- "What software do I need in order to participate?"
- "How do I log in to the work machines?"
objectives:
- "Ensure access to needed hardware and software."
- "Update passwords."
- "Update cheat sheet with necessary info."
- "Download needed files for the workshop."
keypoints:
- "Update passwords."
- "Store needed information so you can always get back in."
- "Make sure files needed for the workshop are downloaded."
permalink: /_episodes/setup
---

In this episode, we will make sure we can log in to the machines at UC and have access 
to necessary software.

## Logging in to a computer on site

Ask the instructor how to log in to a computer on site.

## Logging in to work machines

We will not actually use these computers to do any work. For us, they are simply
convenient portals to the machines that will actually run our analysis code.
To connect to the work machines, open Terminal (Mac) or Command Prompt (Windows)
on the local machine and type the command(s) the instructor gives you.
You will learn more about what these commands mean in future episodes;
for now, it is enough to check that they work.
Make sure to confirm with the instructor that you have successfully connected.

## Updating passwords

You should update your password the first time you log in to the work machines.
You can do this by running the command

```bash
passwd
```

and following the instructions. Make sure to store your new password in a safe place.
Some advice on setting good passwords can be found [here](https://www.cisa.gov/secure-our-world/use-strong-passwords).

## Setting up the environment

Once connected to the work machines, we need to ensure we have access to the right
software. This is done by setting the "environment", which defines what software
we can run from the command line. We will manage our environment using a tool called
"[conda](https://docs.conda.io/en/latest/)". The details of how all this works are not
important for us right now; we just need the magic command

```bash
conda activate Feb2024_388
```

This should ensure you have access to all the software you need.
You will need to run this command every time you log in.

## Jupyter notebooks

We will be using Jupyter notebooks to write and run our code, at least at first.
We will learn more about what these are and how they work in future episodes, but for
now, make sure you can open one by running the command

```bash
jupyter notebook --no-browser --port <your port number>
```

(The instructor will tell you what port to use.)
You should see a message including something like

```text
    Or copy and paste one of these URLs:
        http://<path>:<your port number>/tree?token=<random sequence>
        http://<alternative path>:<your port number>/tree?token=<random sequence>
```

Open a browser on the local machine and type in one of these URLs. You should be taken
to a page that looks like this:

<figure>
<img src="{{site.baseurl}}/fig/Jupyter_landing.png" title="The Jupyter landing page you should see when you first run it. You may or may not see some files in the browser, depending on what you have saved in the directory from which you launched the notebook."/>
</figure>

If this is not what you see, ask the instructor for help.

When you are done:

1. Close your browser window.
2. Return to Terminal (Mac) or Command Prompt (Windows) and press <kbd>^</kbd>+<kbd>C</kbd> (control-C, Mac) or <kbd>Ctrl</kbd>+<kbd>C</kbd> (Windows), then confirm. This will stop the server running the Jupyter notebook.

> ## Always close the browser window first
>
> When you are done working, make sure to close the browser window *before* stopping the
> server. If you stop the server first, you may be inundated with messages like
> `channel 3: open failed: connect failed: Connection refused`.
> If this happens, don't worry; just restart the server, make sure the browser window is
> closed, and then stop the server again (making sure to use the same port as before).
{: .callout}

> ## Save your work
>
> Shutting down the server will *not* automatically save your progress. Make sure to
> save whatever you've been working on *before* shutting down the notebook server.
> (Closing the browser window doesn't affect your work.)
{: .caution}

## Sign out

**Always sign out when leaving for lunch or at the end of the day.**
After closing any Jupyter notebooks you have open, as described
[above](#jupyter-notebooks), run the command

```bash
exit
```

or press <kbd>^</kbd>+<kbd>D</kbd> (control-D, Mac) or <kbd>Ctrl</kbd>+<kbd>D</kbd> (Windows) to log
out of the work machine. Then, make sure to log out of the local computer as well.

> ## Always sign out
>
> It's a security risk to leave yourself logged in to a computer that someone else could
> access. Always always always log out when you're done.
{: .caution}

## Remembering what to do

Your instructor should have provided you with a cheat sheet detailing the essential
steps above. Take a moment to update it with any clarifications or additional
information that could help you remember what to do, e.g., the URL for this website.

## Downloading files

The lessons in this introductory week will refer to sets of files. If they have not
already been downloaded, you can download them by running the following set of commands
after logging in to your work machine (as described [above](#logging-in-to-work-machines)):

```bash
cd
mkdir -p 2024-06-24-UCQuarkNet/lesson-files
cd 2024-06-24-UCQuarkNet/lesson-files
wget https://swcarpentry.github.io/shell-novice/data/shell-lesson-data.zip
unzip shell-lesson-data.zip
wget https://datacarpentry.org/python-ecology-lesson/data/portal-teachingdb-master.zip
unzip portal-teachingdb-master.zip
mv portal-teachingdb-master python-lesson-data
rm *.zip
cd
```

{% include links.md %}