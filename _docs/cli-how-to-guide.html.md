---
title: Bitrise CommandLine Interface How to Guide
---

# Installing Bitrise CommandLine Interface

Yes!!! Our CommandLine Interface is now available via [Homebrew](https://github.com/Homebrew/homebrew/tree/master/share/doc/homebrew#readme)! You simply have to call the ```brew install bitrise``` command in your terminal. And BOOM! you can start using it right away!

If you choose to go the old fashioned way just check the [releases site](https://github.com/bitrise-io/bitrise/releases) for instructions.

## Setting up Bitrise

When you are done with the installation you have to run the ```bitrise setup``` command in the terminal and you are ready to use bitrise-cli! This command checks and installs every needed dependency to run your awesome Workflows.

## Your first project

Now everything is ready for you to take it for a spin! You can see the available commands if you simply type ```bitrise help``` in the terminal.

Let's start with ```bitrise init```

The command first prints our awesome looking logo - don't feel ashamed to stop and stare for a few seconds, we also stared in awe for quite some time when we first saw it - and now let's get down to business! As first step enter the project title, next is the primary development branch.

![Success](images/success.gif "Success")

Great success! Now all that's left is give it a test run! The previous command created a bitrise.yml file in the current directory with a simple workflow. Just type ```bitrise run primary``` and watch bitrise do the magic.
The file contains a simple workflow that only has the step called [script](https://github.com/bitrise-io/bitrise-steplib/tree/master/steps/script) from our [steplib](https://github.com/bitrise-io/steps-script) (this step can be used to run a simple script). In this case it writes ```Welcome to Bitrise!``` to the terminal.

## Creating your own workflow

Feel free to check the example workflows that we created to show you a small proportion of the endless possibilities in Bitrise CLI. You can view them in the [bitrise repository](https://github.com/bitrise-io/bitrise).

Let's try and create one of the tutorial workflows together, shall we?

We'll start off with the ```bitrise.yml``` that the init command created. Open it and take a look.

## The bitrise.yml

As you can see ```bitrise.yml``` contains a header that consists of two information, the ```format version``` that indicates the version and the ```default_step_lib_source``` that shows which step library is used. The default is [https://github.com/bitrise-io/bitrise-steplib](https://github.com/bitrise-io/bitrise-steplib) feel free to look around and experiment with the steps.

There are two main parts of the ```bitrise.yml``` the ```app``` that is filled from the information that you provided during the ```init``` command and the ```workflows```.

You guessed it right, the ```workflows``` contains your Workflows for the given application. Currently there's one Workflow with the ```primary``` id. You can use this or you can create another using the ancient art of copy-paste, but don't forget to rename the pasted one to something else eg.: ```myflippinawesomewf```.

As you can see there are plenty of extra fields like ```summary```, ```title```, ```before_run``` etc. For now we don't need these, feel free to delete them or just leave them empty. Your ```myflippinawesomewf``` should look something like this:

    myflippinawesomewf:
      steps:
      - script:
          title: Hello Bitrise!
          inputs:
          - content: |-
              #!/bin/bash
              echo "Welcome to Bitrise!"

You can try running it again with ```bitrise run myflippinawesomewf```. Let's carry on and add that [timestamp step](https://github.com/bitrise-io/bitrise-steplib/tree/master/steps/timestamp/0.9.0) that is used in the ```steps-and-workflows``` tutorial.
Let's stick to a minimalist approach when adding the [timestamp step](https://github.com/bitrise-io/bitrise-steplib/tree/master/steps/timestamp/0.9.0) and only add ```timestamp``` az a new step in your Workflow. This will tell bitrise to search the default steplib for a step called ```timestamp```, download the bash script of the step and run it. You should always pay attention to the indentation! It is crucial to keep your yml well formatted to get the correct functionality.
Now the workflow should look like this:

    myflippinawesomewf:
      steps:
      - script:
          title: Hello Bitrise!
          inputs:
          - content: |-
              #!/bin/bash
              echo "Welcome to Bitrise!"
      - timestamp:

Great job! Now let's see what happens when we run the workflow! We can see the ```Generate time``` step title and that it finished with Success. But what time is it? Let's do something about this and take a look at the timestamp step's yml.

> When you are adding a new step to your Workflow always check the yml first to see the inputs that it requires and the output it generates.

If you take a look at the yml you can see that it creates two outputs with the given timestamp:

    outputs:
      - UNIX_TIMESTAMP:
        opts:
          title: unix style
      - ISO_DATETIME:
        opts:
          title: iso 8601 (RFC3339Nano)

The outputs section tells us that we can access the generated timestamp using via the ISO_DATETIME or the UNIX_TIMESTAMP environment variables. So let's give it a try and print these values simply by adding another script and echo-ing both of the variables. The workflow should look similar to this:

    myflippinawesomewf:
      steps:
      - script:
          title: Hello Bitrise!
          inputs:
          - content: |-
              #!/bin/bash
              echo "Welcome to Bitrise!"
      - timestamp:
      - script:
          title: "print time"
          inputs:
          - content: |
              #/bin/bash
              set -e
              echo "ISO_DATETIME: ${ISO_DATETIME}"
              echo "UNIX_TIMESTAMP: ${UNIX_TIMESTAMP}"

Now even the geeky ones will know the time thanks to your UNIX_TIMESTAMP output! Great job! Your first workflow is done!

But before you close this tab let's put some twist in it shall we?

As the saying goes: "It's good to know the time, but flooding a Slack channel with the current time is the best!"

So our next step will be adding a Slack Step to notify a channel about the current time! **Keeping your private information is very important for us so we'll use the `.bitrise.secrets.yml` file for the Slack informations. Make sure to include the .bitrise.secrets.yml file in your .gitignore** Let's open the file (or create it if there's no `.bitrise.secrets.yml`). The following inputs are needed for running the slack-message Step:

- SLACK_WEBHOOK_URL:
- SLACK_CHANNEL:
- SLACK_FROM_NAME:
- SLACK_ERROR_FROM_NAME:
- SLACK_ERROR_MESSAGE_TEXT:

Now that you added the required inputs add the slack-message Step to the Workflow. When you're done the Workflow should look something like this:

    myflippinawesomewf:
      steps:
      - script:
          title: Hello Bitrise!
          inputs:
          - content: |-
              #!/bin/bash
              echo "Welcome to Bitrise!"
      - timestamp:
      - script:
          title: "print time"
          inputs:
          - content: |
              #/bin/bash
              set -e
              echo "ISO_DATETIME: ${ISO_DATETIME}"
              echo "UNIX_TIMESTAMP: ${UNIX_TIMESTAMP}"
      - slack-message:
          inputs:
          - SLACK_MESSAGE_TEXT: "ISO_DATETIME: ${ISO_DATETIME}"

Well that's it! Run the workflow and wait for that awesome Slack message! Happy building!
