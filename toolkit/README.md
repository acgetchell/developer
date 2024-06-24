- [Write Codeblocks](#write-codeblocks)
  - [Usage](#usage)
- [Check CLI Output](#check-cli-output)
  - [Usage](#usage-1)

## Supporting Documentation

If you are using this toolkit to add/edit the current documentation, please read the [contributing documentation](https://developer.fermyon.com/spin/v2/contributing-docs) page (and perhaps leave it open for reference when forking, cloning, committing, etc.).

## Prerequisite

Ensure that you have installed Python 3.10 or later on your system. You can check your Python version by running:

```bash
python3 --version
```

### Install Dependencies

Change into an arbitrary working directory so as not to add versioned files to this repository. The files created by this toolkit are auxiliary and are manually inserted into the versioned documentation files. We will use the system's home directory for demonstration purposes (and assume that you have the developer repo cloned into your home directory i.e. `~/developer`):

```bash
cd ~
```

Set up and activate a Python virtual environment and then install the dependencies:

```bash
# Create venv
python3 -m venv venv-dir
# Activate venv
source venv-dir/bin/activate
# Install requests library
pip3 install requests
```

# Write Codeblocks

The `update_spin_cli_reference.py` script will fetch the current [Spin CLI Reference](https://developer.fermyon.com/spin/v2/cli-reference) document from the web and write a fresh copy of the source markdown file (a new file called `updated_markdown.md` with the new version of code blocks added). The fresh markdown file, generated by this script, is the basis of a PR that contains the CLI commands for the new version of Spin.

## Usage

Edit the version numbers in the file i.e. `current_version="v2.6", new_version="v2.7"` and run the script:

```bash
python3 developer/toolkit/update_spin_cli_reference.py
```

# Check CLI Output

The following `check_cli_output.py` script will tell you what the changes are (between versions) these scripts are designed to quickly kick off the edit, review, approve, and merge process at the time of release.

How does this work?

The `check_cli_output.py` script runs `spin --help` for all subcommands recursively. This is useful for quickly detecting changes in the Spin CLI's output between different versions (i.e. when updating the Spin CLI Reference page in the documentation during each release).

## Usage

We:
- create a variable for each version
- create two new directories
- copy the script into each directory (this makes the script's output identical - aside from actual changes)
- install the specific version of Spin in each directory
- run the script using that specific version
- diff the results

```bash
cd ~
export SPIN_LATEST=v2.6.0
export SPIN_OLD=v2.5.0
mkdir $SPIN_OLD
mkdir $SPIN_LATEST
cp -rp check_cli_output.py $SPIN_OLD
cp -rp check_cli_output.py $SPIN_LATEST
cd ~
cd $SPIN_OLD
curl -fsSL https://developer.fermyon.com/downloads/install.sh | bash -s -- -v $SPIN_OLD
python3 check_cli_output.py > $SPIN_OLD
cd ../ 
cd $SPIN_LATEST
curl -fsSL https://developer.fermyon.com/downloads/install.sh | bash -s -- -v $SPIN_LATEST
python3 check_cli_output.py > $SPIN_LATEST
cd ../
diff $SPIN_OLD/$SPIN_OLD $SPIN_LATEST/$SPIN_LATEST
```

The output will show old content on the `<` prefixed lines and new content on the `>` prefixed lines. If there have been no changes to the CLI (other than the version and checksum) you will see something similar to the following:

```bash
< Output: spin 2.5.0 (83eb68d 2024-05-08)

> Output: spin 2.6.0 (a4ddd39 2024-06-20)
```

To clean up, run the following:

```bash
rm -rf $SPIN_OLD
rm -rf $SPIN_LATEST
```