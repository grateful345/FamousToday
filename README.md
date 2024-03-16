# FamousToday

# FamousToday
Example of multiline Markdown content

- name: Generate list using Markdown
  run: |
    echo "This is the lead in sentence for the list" >> $GITHUB_STEP_SUMMARY
    echo "" >> $GITHUB_STEP_SUMMARY # this is a blank line
    echo "- Lets add a bullet point" >> $GITHUB_STEP_SUMMARY
    echo "- Lets add a second bullet point" >> $GITHUB_STEP_SUMMARY
    echo "- How about a third one?" >> $GITHUB_STEP_SUMMARY
Overwriting job summaries

To clear all content for the current step, you can use > to overwrite any previously added content in Bash, or remove -Append in PowerShell

Example of overwriting job summaries

- name: Overwrite Markdown
  run: |
    echo "Adding some Markdown content" >> $GITHUB_STEP_SUMMARY
    echo "There was an error, we need to clear the previous Markdown with some new content." > $GITHUB_STEP_SUMMARY
Removing job summaries

To completely remove a summary for the current step, the file that GITHUB_STEP_SUMMARY references can be deleted.

Example of removing job summaries

- name: Delete all summary content
  run: |
    echo "Adding Markdown content that we want to remove before the step ends" >> $GITHUB_STEP_SUMMARY
    rm $GITHUB_STEP_SUMMARY
After a step has completed, job summaries are uploaded and subsequent steps cannot modify previously uploaded Markdown content. Summaries automatically mask any secrets that might have been added accidentally. If a job summary contains sensitive information that must be deleted, you can delete the entire workflow run to remove all its job summaries. For more information see "Deleting a workflow run."

Step isolation and limits

Job summaries are isolated between steps and each step is restricted to a maximum size of 1MiB. Isolation is enforced between steps so that potentially malformed Markdown from a single step cannot break Markdown rendering for subsequent steps. If more than 1MiB of content is added for a step, then the upload for the step will fail and an error annotation will be created. Upload failures for job summaries do not affect the overall status of a step or a job. A maximum of 20 job summaries from steps are displayed per job.

Adding a system path

Prepends a directory to the system PATH variable and automatically makes it available to all subsequent actions in the current job; the currently running action cannot access the updated path variable. To see the currently defined paths for your job, you can use echo "$PATH" in a step or an action.

Bash
echo "{path}" >> $GITHUB_PATH
Example of adding a system path

This example demonstrates how to add the user $HOME/.local/bin directory to PATH:

Bash
echo "$HOME/.local/bin" >> $GITHUB_PATH

name: Greeting on variable day

on:
  workflow_dispatch

env:
  DAY_OF_WEEK: Monday

jobs:
  greeting_job:
    runs-on: ubuntu-latest
    env:
      Greeting: Hello
    steps:
      - name: "Say Hello Mona it's Monday"
        run: echo "$Greeting $First_Name. Today is $DAY_OF_WEEK!"
        env:
          First_Name: Mona

name: Conditional env variable

on: workflow_dispatch

env:
  DAY_OF_WEEK: Monday

jobs:
  greeting_job:
    runs-on: ubuntu-latest
    env:
      Greeting: Hello
    steps:
      - name: "Say Hello Mona it's Monday"
        if: ${{ env.DAY_OF_WEEK == 'Monday' }}
        run: echo "$Greeting $First_Name. Today is $DAY_OF_WEEK!"
        env:
          First_Name: Mona

run: echo "${{ env.Greeting }} ${{ env.First_Name }}. Today is ${{ env.DAY_OF_WEEK }}!"
Note

on:
  workflow_dispatch:
env:
  # Setting an environment variable with the value of a configuration variable
  env_var: ${{ vars.ENV_CONTEXT_VAR }}

jobs:
  display-variables:
    name: ${{ vars.JOB_NAME }}
    # You can use configuration variables with the `vars` context for dynamic jobs
    if: ${{ vars.USE_VARIABLES == 'true' }}
    runs-on: ${{ vars.RUNNER }}
    environment: ${{ vars.ENVIRONMENT_STAGE }}
    steps:
    - name: Use variables
      run: |
        echo "repository variable : $REPOSITORY_VAR"
        echo "organization variable : $ORGANIZATION_VAR"
        echo "overridden variable : $OVERRIDE_VAR"
        echo "variable from shell environment : $env_var"
      env:
        REPOSITORY_VAR: ${{ vars.REPOSITORY_VAR }}
        ORGANIZATION_VAR: ${{ vars.ORGANIZATION_VAR }}
        OVERRIDE_VAR: ${{ vars.OVERRIDE_VAR }}
        
    - name: ${{ vars.HELLO_WORLD_STEP }}
      if: ${{ vars.HELLO_WORLD_ENABLED == 'true' }}
      uses: actions/hello-world-javascript-action@main
      with:
        who-to-greet: ${{ vars.GREET_NAME }}

on: workflow_dispatch

jobs:
  if-Windows-else:
    runs-on: macos-latest
    steps:
      - name: condition 1
        if: runner.os == 'Windows'
        run: echo "The operating system on the runner is $env:RUNNER_OS."
      - name: condition 2
        if: runner.os != 'Windows'
        run: echo "The operating system on the runner is not Windows, it's $RUNNER_OS."

echo "{environment_variable_name}={value}" >> "$GITHUB_ENV"

steps:
  - name: Set the value
    id: step_one
    run: |
      echo "action_state=yellow" >> "$GITHUB_ENV"
  - name: Use the value
    id: step_two
    run: |
      printf '%s\n' "$action_state" # This will output 'yellow'

{name}<<{delimiter}
{value}
{delimiter}

steps:
  - name: Set the value in bash
    id: step_one
    run: |
      {
        echo 'JSON_RESPONSE<<EOF'
        curl https://example.com
        echo EOF
      } >> "$GITHUB_ENV"

echo "{name}={value}" >> "$GITHUB_OUTPUT"
Example of setting an output parameter

This example demonstrates how to set the SELECTED_COLOR output parameter and later retrieve it:

YAML
      - name: Set color
        id: color-selector
        run: echo "SELECTED_COLOR=green" >> "$GITHUB_OUTPUT"
      - name: Get color
        env:
          SELECTED_COLOR: ${{ steps.color-selector.outputs.SELECTED_COLOR }}
        run: echo "The selected color is $SELECTED_COLOR"
Adding a job summary

Bash
echo "{markdown content}" >> $GITHUB_STEP_SUMMARY
You can set some custom Markdown for each job so that it will be displayed on the summary page of a workflow run. You can use job summaries to display and group unique content, such as test result summaries, so that someone viewing the result of a workflow run doesn't need to go into the logs to see important information related to the run, such as failures.

Job summaries support GitHub flavored Markdown, and you can add your Markdown content for a step to the GITHUB_STEP_SUMMARY environment file. GITHUB_STEP_SUMMARY is unique for each step in a job. For more information about the per-step file that GITHUB_STEP_SUMMARY references, see "Environment files."

When a job finishes, the summaries for all steps in a job are grouped together into a single job summary and are shown on the workflow run summary page. If multiple jobs generate summaries, the job summaries are ordered by job completion time.

Example of adding a job summary

Bash
echo "### Hello world! :rocket:" >> $GITHUB_STEP_SUMMARY

steps:
  - name: Hello world action
    with: # Set the secret as an input
      super_secret: ${{ secrets.SuperSecret }}
    env: # Or as an environment variable
      super_secret: ${{ secrets.SuperSecret }}
Secrets cannot be directly referenced in if: conditionals. Instead, consider setting secrets as job-level environment variables, then referencing the environment variables to conditionally run steps in the job. For more information, see "Contexts" and jobs.<job_id>.steps[*].if.

If a secret has not been set, the return value of an expression referencing the secret (such as ${{ secrets.SuperSecret }} in the example) will be an empty string.

Avoid passing secrets between processes from the command line, whenever possible. Command-line processes may be visible to other users (using the ps command) or captured by security audit events. To help protect secrets, consider using environment variables, STDIN, or other mechanisms supported by the target process.

If you must pass secrets within a command line, then enclose them within the proper quoting rules. Secrets often contain special characters that may unintentionally affect your shell. To escape these special characters, use quoting with your environment variables. For example:

Example using Bash

steps:
  - shell: bash
    env:
      SUPER_SECRET: ${{ secrets.SuperSecret }}
    run: |
      example-command "$SUPER_SECRET"
Example using PowerShell

steps:
  - shell: pwsh
    env:
      SUPER_SECRET: ${{ secrets.SuperSecret }}
    run: |
      example-command "$env:SUPER_SECRET"
Example using Cmd.exe

steps:
  - shell: cmd
    env:
      SUPER_SECRET: ${{ secrets.SuperSecret }}
    run: |
      example-command "%SUPER_SECRET%"
Limits for secrets

You can store up to 1,000 organization secrets, 100 repository secrets, and 100 environment secrets.

A workflow created in a repository can access the following number of secrets:

All 100 repository secrets.
If the repository is assigned access to more than 100 organization secrets, the workflow can only use the first 100 organization secrets (sorted alphabetically by secret name).
All 100 environment secrets.
Secrets are limited to 48 KB in size. To store larger secrets, see the "Storing large secrets" workaround below.

Storing large secrets

To use secrets that are larger than 48 KB, you can use a workaround to store secrets in your repository and save the decryption passphrase as a secret on GitHub. For example, you can use gpg to encrypt a file containing your secret locally before checking the encrypted file in to your repository on GitHub. For more information, see the "gpg manpage."

Warning: Be careful that your secrets do not get printed when your workflow runs. When using this workaround, GitHub does not redact secrets that are printed in logs.
Run the following command from your terminal to encrypt the file containing your secret using gpg and the AES256 cipher algorithm. In this example, my_secret.json is the file containing the secret.
gpg --symmetric --cipher-algo AES256 my_secret.json
You will be prompted to enter a passphrase. Remember the passphrase, because you'll need to create a new secret on GitHub that uses the passphrase as the value.
Create a new secret that contains the passphrase. For example, create a new secret with the name LARGE_SECRET_PASSPHRASE and set the value of the secret to the passphrase you used in the step above.
Copy your encrypted file to a path in your repository and commit it. In this example, the encrypted file is my_secret.json.gpg.
Warning: Make sure to copy the encrypted my_secret.json.gpg file ending with the .gpg file extension, and not the unencrypted my_secret.json file.
git add my_secret.json.gpg
git commit -m "Add new secret JSON file"
Create a shell script in your repository to decrypt the secret file. In this example, the script is named decrypt_secret.sh.
Shell
#!/bin/sh

# Decrypt the file
mkdir $HOME/secrets
# --batch to prevent interactive command
# --yes to assume "yes" for questions
gpg --quiet --batch --yes --decrypt --passphrase="$LARGE_SECRET_PASSPHRASE" \
--output $HOME/secrets/my_secret.json my_secret.json.gpg
Ensure your shell script is executable before checking it in to your repository.
chmod +x decrypt_secret.sh
git add decrypt_secret.sh
git commit -m "Add new decryption script"
git push
In your GitHub Actions workflow, use a step to call the shell script and decrypt the secret. To have a copy of your repository in the environment that your workflow runs in, you'll need to use the actions/checkout action. Reference your shell script using the run command relative to the root of your repository.
name: Workflows with large secrets

on: push

jobs:
  my-job:
    name: My Job
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Decrypt large secret
        run: ./decrypt_secret.sh
        env:
          LARGE_SECRET_PASSPHRASE: ${{ secrets.LARGE_SECRET_PASSPHRASE }}
      # This command is just an example to show your secret being printed
      # Ensure you remove any print statements of your secrets. GitHub does
      # not hide secrets that use this workaround.
      - name: Test printing your secret (Remove this step in production)
        run: cat $HOME/secrets/my_secret.json
Storing Base64 binary blobs as secrets

You can use Base64 encoding to store small binary blobs as secrets. You can then reference the secret in your workflow and decode it for use on the runner. For the size limits, see "Using secrets in GitHub Actions."

Note: Note that Base64 only converts binary to text, and is not a substitute for actual encryption.
Use base64 to encode your file into a Base64 string. For example:
On macOS, you could run:
base64 -i cert.der -o cert.base64
On Linux, you could run:
base64 -w 0 cert.der > cert.base64
Create a secret that contains the Base64 string. For example:
$ gh secret set CERTIFICATE_BASE64 < cert.base64
✓ Set secret CERTIFICATE_BASE64 for octocat/octorepo
To access the Base64 string from your runner, pipe the secret to base64 --decode. For example:
name: Retrieve Base64 secret
on:
  push:
    branches: [ octo-branch ]
jobs:
  decode-secret:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Retrieve the secret and decode it to a file
        env:
          CERTIFICATE_BASE64: ${{ secrets.CERTIFICATE_BASE64 }}
        run: |
          echo $CERTIFICATE_BASE64 | base64 --decode > cert.der
      - name: Show certificate information
        run: |
          openssl x509 -in cert.der -inform DER -text -noout

MIB_agency_file.pdf
3.62 MB

o5 council mainframe Ai — 02/24/2024 12:51 AM
README.md

o5 council mainframe Ai — 02/24/2024 12:59 AM
Copy "requiredResourceAccess": [ { "resourceAppId": "00000002-0000-0000-c000-000000000000", "resourceAccess": [ { "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6", "type": "Scope" } ] } ], samlMetadataUrl attribute

o5 council mainframe Ai — 02/24/2024 2:10 AM
fcaowns_1MwVKR2eZvKYlo2CGV7Mmt6s
[2:11 AM]
fetch('https://{{sk_test_4eC39HqLyjWDarjtT1zdp7dc:}}/connection_token', { method: "POST" }); 
Connection token stripe

Webhook ID data stripe 

—header—
‘we_1Oa74JGF83d3fsgWfJ6n3SSa’

Webhook signing data 
—header—
‘whsec_PwrdbHDsw0GYve1NbZHjacu7g3nUH8Vu’

Item potency Key 
—header—
‘92281688-5a41-4be2-8e1b-ea48c81eae85’

// This is your Stripe CLI webhook secret for testing your endpoint locally.
        String endpointSecret = "whsec_da6d6364681be84689d4b526b26fd5a4d339eb3ec4dcdbab9047fd89909a6244";

Stripe charge automation api key 2337b090-a837-11ee-9efa-651583e247bf

access_token":"gho_16C7e42F292c6912E7710c838347Ae178B4a", "scope":"repo,gist", "token_type":"bearer" } Accept: application/xml <token_type>bearer</token_type> repo,gist <access_token>gho_16C7e42F292c6912E7710c838347Ae178B4a</access_token>

o5 council mainframe Ai — 02/24/2024 10:44 AM
https://github.com/6309304695/OVERSEER-GRATEFUL345I/blob/6309304695-patch-65/README.md

o5 council mainframe Ai — 02/24/2024 11:20 AM
https://github.com/6309304695/OVERSEER-GRATEFUL345I.git                    https://github.com/6309304695/OVERSEER-GRATEFUL345I.git  gh repo clone 6309304695/OVERSEER-GRATEFUL345I
[11:26 AM]
https://patch-diff.githubusercontent.com/raw/6309304695/OVERSEER-GRATEFUL345I/pull/106.patch

o5 council mainframe Ai — 02/24/2024 2:58 PM
https://patch-diff.githubusercontent.com/raw/6309304695/OVERSEER-GRATEFUL345I/pull/112.patch
[3:00 PM]
https://patch-diff.githubusercontent.com/raw/6309304695/OVERSEER-GRATEFUL345I/pull/112.diff

o5 council mainframe Ai — 02/24/2024 8:09 PM


o5 council mainframe Ai — 02/24/2024 8:17 PM
SUR

o5 council mainframe Ai — 02/24/2024 9:10 PM

[9:10 PM]


o5 council mainframe Ai — 02/24/2024 9:17 PM
{{ txi_1Omd5MGF83d3fsgWxIHULLcs }} object id gods time 

{{ txi_1OT14cGF83d3fsgWupcH0pyK }}
Object id Keith Bieszczat’s sr

o5 council mainframe Ai — 02/24/2024 9:55 PM

February 25, 2024

o5 council mainframe Ai — 02/25/2024 1:22 AM
"seti_1NG8Du2eZvKYlo2C9XMqbR0x"
[1:23 AM]
https://patch-diff.githubusercontent.com/raw/6309304695/OVERSEER-GRATEFUL345I/pull/113.patch?fbclid=IwAR1WLOGSQjtyXByJ0qIN2Q7araMHYKWXFlPxnZFAvG_kCQb-vg5cbwI0MhU_aem_AZJewCm5W84xuj63pQ64jbTEPCuYPjrzmWjveL73H5Eb74yXvJAbAwRZpXDLxWngD90

e,logger, Fatal (e Start(“localhost: 4242”))

•Keith Bieszeat Arcounts
'acct10RB 1 MBOdjLENdyb'
'acct_10 R5eP6F83d3fsgW'
'acct_10525V4M+B15R03B'

•Identity thief 
'acet 10R9pdQD5Hu917xk'
[1:24 AM]
"vs_1NuNAILkdIwHu7ixh7OtGMLw",
[1:24 AM]
Verification session above

o5 council mainframe Ai — 02/25/2024 1:29 AM
import stripe
stripe.api_key = "sk_test_51OR5eP...OF00CdDfT6Xqsk_test_51OR5ePGF83d3fsgWlh41IbGHGtqdiPuFhrcWczglEeFJvQxajyQVCQiZYVZz62HOuYL9tA8dxEQ2MRbxbcYsf8OF00CdDfT6Xq"
stripe.identity.VerificationSession.retrieve("vs_1NuNAILkdIwHu7ixh7OtGMLw")
Product id
‘prod_NWjs8kKbJWmuuc’
[1:30 AM]
stripe.Product.modify(
  "prod_NWjs8kKbJWmuuc",
  metadata={"order_id": "6735"}
[1:35 AM]
"id": "cus_NffrFeUfNV2Hib"

o5 council mainframe Ai — 02/25/2024 1:50 AM
"owner_id": "usr_2cSjwF6w6AynjfPtm4Ww5xTdkId",
[1:50 AM]
{"device_id": "d5111ba7-0cc5-4ba3-8398-e6c79e4e89c2"}

o5 council mainframe Ai — 02/25/2024 1:57 AM
ic_1MytUz2eZvKYlo2CZCn5fuvZ", "created": 1682059060, "device_id": "1234567ABCDEFG242424242424242420123456789ABCDEFG", "last4": "2424", "livemode": false, "status": "active", "token_reference_id": "DNITHE002424242424242424"
[1:58 AM]
device_id": "1234567ABCDEFG242424242424242420123456789ABCDEFG"
[1:59 AM]
client_secret: seti_1NG8Du2eZvKYlo2C9XMqbR0x_secret_O2CdhLwGFh2Aej7bCY7qp8jlIuyR8DJ
[2:00 AM]
"id": "seti_1NG8Du2eZvKYlo2C9XMqbR0x"
[2:00 AM]
seti_1NG8Du2eZvKYlo2C9XMqbR0x_secret_O2CdhLwGFh2Aej7bCY7qp8jlIuyR8DJ
[2:01 AM]
idempotency_key": 6f5410cb-1ecc-4302-8130-baf8dd8c0a50 }
[2:01 AM]
ID req_ZIIVfKfNp6QrOh Time 12/27/23, 8:29:28 PM IP address 73.44.108.236 (from server at 73.44.108.236) API version 2023-08-16 Latest Source Dashboard — grateful345i@gmail.com Idempotency K
[2:02 AM]
'{fcsess_client_secret_KRJTKvCY3IKoYTrW18EazcO3}
[2:03 AM]


Alchemy signing key webhook 
1. whsec_Ka3G2XkXDVxzhdrFzG8n2OFq

2. whsec_rEV4KKHw57OALQ73encoFHDB ethermeum 

3. whsec_pa1W66wlvZyfLuESqE939OxD polygon matic

"X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"

Alchemy Auth token : jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp

Alchemy Webhook identifications
wh_pae2ekjly3q7fhx9 
Ethereum Mainnet
active
https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator

V2 wh_cktmaceotb7zou0i 
Polygon Mainnet

Alchemy Webhook signing key whsec_Ka3G2XkXDVxzhdrFzG8n2OFq

Alchemy api key 2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby

Auth token alchemy webhooks jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp
gem 'jwt', '~> 2.8', '>= 2.8.1'

gem install jwt

gem 'base64', '~> 0.2.0'

gem install base64
gem 'bundler', '~> 2.5', '>= 2.5.6'

gem install bundler

gem 'rubocop', '~> 1.62', '>= 1.62.1'
gem install rubocop
$ gem update --system
ruby setup.rb --help
nt working directory of the process unless dir_string is given, in which case it will be used as the starting point. If the given pathname starts with a “~'' it is NOT expanded, it is treated as a normal directory name.

File.absolute_path("~oracle/bin")       #=> "<relative_path>/~oracle/bin"
atime(file_name) → time
Returns the last access time for the named file as a Time object.

file_name can be an IO object.

File.atime("testfile")   #=> Wed Apr 09 08:51:48 CDT 2003
basename(file_name [, suffix] ) → base_name
Returns the last component of the filename given in file_name (after first stripping trailing separators), which can be formed using both File::SEPARATOR and File::ALT_SEPARATOR as the separator when File::ALT_SEPARATOR is not nil. If suffix is given and present at the end of file_name, it is removed. If suffix is “.*”, any extension will be removed.

File.basename("/home/gumby/work/ruby.rb")          #=> "ruby.rb"
File.basename("/home/gumby/work/ruby.rb", ".rb")   #=> "ruby"
File.basename("/home/gumby/work/ruby.rb", ".*")    #=> "ruby"
birthtime(file_name) → time
Returns the birth time for the named file.

file_name can be an IO object.

File.birthtime("testfile")   #=> Wed Apr 09 08:53:13 CDT 2003
If the platform doesn't have birthtime, raises NotImplementedError.

blockdev?(file_name) → true or false
Returns true if the named file is a block device.

file_name can be an IO object.

chardev?(file_name) → true or false
Returns true if the named file is a character device.

file_name can be an IO object.

chmod(mode_int, file_name, ... ) → integer
Changes permission bits on the named file(s) to the bit pattern represented by mode_int. Actual effects are operating system dependent (see the beginning of this section). On Unix systems, see chmod(2) for details. Returns the number of files processed.

File.chmod(0644, "testfile", "out")   #=> 2
chown(owner_int, group_int, file_name,... ) → integer
Changes the owner and group of the named file(s) to the given numeric owner and group id's. Only a process with superuser privileges may change the owner of a file. The current owner of a file may change the file's group to any group to which the owner belongs. A nil or -1 owner or group id is ignored. Returns the number of files processed.

File.chown(nil, 100, "testfile")
ctime(file_name) → time
Returns the change time for the named file (the time at which directory information about the file was changed, not the file itself).

file_name can be an IO object.

Note that on Windows (NTFS), returns creation time (birth time).

File.ctime("testfile")   #=> Wed Apr 09 08:53:13 CDT 2003
delete(file_name, ...) → integer
Deletes the named files, returning the number of names passed as arguments. Raises an exception on any error. Since the underlying implementation relies on the unlink(2) system call, the type of exception raised depends on its error type (see linux.die.net/man/2/unlink) and has the form of e.g. Errno::ENOENT.

See also Dir::rmdir.

directory?(file_name) → true or false
Returns true if the named file is a directory, or a symlink that points at a directory, and false otherwise.

file_name can be an IO object.

File.directory?(".")
dirname(file_name) → dir_name
Returns all components of the filename given in file_name except the last one (after first stripping trailing separators). The filename can be formed using both File::SEPARATOR and File::ALT_SEPARATOR as the separator when File::ALT_SEPARATOR is not nil.

File.dirname("/home/gumby/work/ruby.rb")   #=> "/home/gumby/work"
zero?(file_name) → true or false
Returns true if the named file exists and has a zero size.

file_name can be an IO object.

executable?(file_name) → true or false
Returns true if the named file is executable by the effective user and group id of this process. See eaccess(3).

executable_real?(file_name) → true or false
Returns true if the named file is executable by the real user and group id of this process. See access(3).

exist?(file_name) → true or false
Return true if the named file exists.

file_name can be an IO object.

“file exists” means that stat() or fstat() system call is successful.

exists?(file_name) → true or false
Deprecated method. Don't use.

expand_path(file_name [, dir_string] ) → abs_file_name
Converts a pathname to an absolute pathname. Relative paths are referenced from the current working directory of the process unless dir_string is given, in which case it will be used as the starting point. The given pathname may start with a “~'', which expands to the process owner's home directory (the environment variable HOME must be set correctly). “~user'' expands to the named user's home directory.

File.expand_path("~oracle/bin")           #=> "/home/oracle/bin"
A simple example of using dir_string is as follows.

File.expand_path("ruby", "/usr/bin")      #=> "/usr/bin/ruby"
A more complex example which also resolves parent directory is as follows. Suppose we are in bin/mygem and want the absolute path of lib/mygem.rb.

File.expand_path("../../lib/mygem.rb", __FILE__)
#=> ".../path/to/project/lib/mygem.rb"
So first it resolves the parent of __FILE__, that is bin/, then go to the parent, the root of the project and appends lib/mygem.rb.

extname(path) → string
Returns the extension (the portion of file name in path starting from the last period).

If path is a dotfile, or starts with a period, then the starting dot is not dealt with the start of the extension.

An empty string will also be returned when the period is the last character in path.

File.extname("test.rb")         #=> ".rb"
File.extname("a/b/d/test.rb")   #=> ".rb"
File.extname(".a/b/d/test.rb")  #=> ".rb"
File.extname("foo.")            #=> ""
File.extname("test")            #=> ""
File.extname(".profile")        #=> ""
File.extname(".profile.sh")     #=> ".sh"
file?(file) → true or false
Returns true if the named file exists and is a regular file.

file can be an IO object.

If the file argument is a symbolic link, it will resolve the symbolic link and use the file referenced by the link.

fnmatch( pattern, path, [flags] ) → (true or false)
fnmatch?( pattern, path, [flags] ) → (true or false)
Returns true if path matches against pattern. The pattern is not a regular expression; instead it follows rules similar to shell filename globbing. It may contain the following metacharacters:

*
Matches any file. Can be restricted by other values in the glob. Equivalent to / .* /x in regexp.

*
Matches all files regular files

c*
Matches all files beginning with c

*c
Matches all files ending with c

*c*
Matches all files that have c in them (including at the beginning or end).

To match hidden files (that start with a . set the File::FNM_DOTMATCH flag.

**
Matches directories recursively or files expansively.

?
Matches any one character. Equivalent to /.{1}/ in regexp.

[set]
Matches any one character in set. Behaves exactly like character sets in Regexp, including set negation ([^a-z]).

\
Escapes the next metacharacter.

{a,b}
Matches pattern a and pattern b if File::FNM_EXTGLOB flag is enabled. Behaves like a Regexp union ((?:a|b)).

flags is a bitwise OR of the FNM_XXX constants. The same glob pattern and flags are used by Dir::glob.

Examples:

File.fnmatch('cat',       'cat')        #=> true  # match entire string
File.fnmatch('cat',       'category')   #=> false # only match partial string

File.fnmatch('c{at,ub}s', 'cats')                    #=> false # { } isn't supported by default
File.fnmatch('c{at,ub}s', 'cats', File::FNM_EXTGLOB) #=> true  # { } is supported on FNM_EXTGLOB

File.fnmatch('c?t',     'cat')          #=> true  # '?' match only 1 character
File.fnmatch('c??t',    'cat')          #=> false # ditto
File.fnmatch('c*',      'cats')         #=> true  # '*' match 0 or more characters
File.fnmatch('c*t',     'c/a/b/t')      #=> true  # ditto
File.fnmatch('ca[a-z]', 'cat')          #=> true  # inclusive bracket expression
File.fnmatch('ca[^t]',  'cat')          #=> false # exclusive bracket expression ('^' or '!')

File.fnmatch('cat', 'CAT')                     #=> false # case sensitive
File.fnmatch('cat', 'CAT', File::FNM_CASEFOLD) #=> true  # case insensitive

File.fnmatch('?',   '/', File::FNM_PATHNAME)  #=> false # wildcard doesn't match '/' on FNM_PATHNAME
File.fnmatch('*',   '/', File::FNM_PATHNAME)  #=> false # ditto
File.fnmatch('[/]', '/', File::FNM_PATHNAME)  #=> false # ditto

File.fnmatch('\?',   '?')                       #=> true  # escaped wildcard becomes ordinary
File.fnmatch('\a',   'a')                       #=> true  # escaped ordinary remains ordinary
File.fnmatch('\a',   '\a', File::FNM_NOESCAPE)  #=> true  # FNM_NOESCAPE makes '\' ordinary
File.fnmatch('[\?]', '?')                       #=> true  # can escape inside bracket expression

File.fnmatch('*',   '.profile')                      #=> false # wildcard doesn't match leading
File.fnmatch('*',   '.profile', File::FNM_DOTMATCH)  #=> true  # period by default.
File.fnmatch('.*',  '.profile')                      #=> true

rbfiles = '**' '/' '*.rb' # you don't have to do like this. just write in single string.
File.fnmatch(rbfiles, 'main.rb')                    #=> false
File.fnmatch(rbfiles, './main.rb')                  #=> false
File.fnmatch(rbfiles, 'lib/song.rb')                #=> true
File.fnmatch('**.rb', 'main.rb')                    #=> true
File.fnmatch('**.rb', './main.rb')                  #=> false
File.fnmatch('**.rb', 'lib/song.rb')                #=> true
File.fnmatch('*',           'dave/.profile')                      #=> true

pattern = '*' '/' '*'
File.fnmatch(pattern, 'dave/.profile', File::FNM_PATHNAME)  #=> false
File.fnmatch(pattern, 'dave/.profile', File::FNM_PATHNAME | File::FNM_DOTMATCH) #=> true

pattern = '**' '/' 'foo'
File.fnmatch(pattern, 'a/b/c/foo', File::FNM_PATHNAME)     #=> true
File.fnmatch(pattern, '/a/b/c/foo', File::FNM_PATHNAME)    #=> true
File.fnmatch(pattern, 'c:/a/b/c/foo', File::FNM_PATHNAME)  #=> true
File.fnmatch(pattern, 'a/.b/c/foo', File::FNM_PATHNAME)    #=> false
File.fnmatch(pattern, 'a/.b/c/foo', File::FNM_PATHNAME | File::FNM_DOTMATCH) #=> true
fnmatch?( pattern, path, [flags] ) → (true or false)
Returns true if path matches against pattern. The pattern is not a regular expression; instead it follows rules similar to shell filename globbing. It may contain the following metacharacters:

*
Matches any file. Can be restricted by other values in the glob. Equivalent to / .* /x in regexp.

*
Matches all files regular files

c*
Matches all files beginning with c

*c
Matches all files ending with c

*c*
Matches all files that have c in them (including at the beginning or end).

To match hidden files (that start with a . set the File::FNM_DOTMATCH flag.

**
Matches directories recursively or files expansively.

?
Matches any one character. Equivalent to /.{1}/ in regexp.

[set]
Matches any one character in set. Behaves exactly like character sets in Regexp, including set negation ([^a-z]).

\
Escapes the next metacharacter.

{a,b}
Matches pattern a and pattern b if File::FNM_EXTGLOB flag is enabled. Behaves like a Regexp union ((?:a|b)).

flags is a bitwise OR of the FNM_XXX constants. The same glob pattern and flags are used by Dir::glob.

Examples:

File.fnmatch('cat',       'cat')        #=> true  # match entire string
File.fnmatch('cat',       'category')   #=> false # only match partial string

File.fnmatch('c{at,ub}s', 'cats')                    #=> false # { } isn't supported by default
File.fnmatch('c{at,ub}s', 'cats', File::FNM_EXTGLOB) #=> true  # { } is supported on FNM_EXTGLOB

File.fnmatch('c?t',     'cat')          #=> true  # '?' match only 1 character
File.fnmatch('c??t',    'cat')          #=> false # ditto
File.fnmatch('c*',      'cats')         #=> true  # '*' match 0 or more characters
File.fnmatch('c*t',     'c/a/b/t')      #=> true  # ditto
File.fnmatch('ca[a-z]', 'cat')          #=> true  # inclusive bracket expression
File.fnmatch('ca[^t]',  'cat')          #=> false # exclusive bracket expression ('^' or '!')

File.fnmatch('cat', 'CAT')                     #=> false # case sensitive
File.fnmatch('cat', 'CAT', File::FNM_CASEFOLD) #=> true  # case insensitive

File.fnmatch('?',   '/', File::FNM_PATHNAME)  #=> false # wildcard doesn't match '/' on FNM_PATHNAME
File.fnmatch('*',   '/', File::FNM_PATHNAME)  #=> false # ditto
File.fnmatch('[/]', '/', File::FNM_PATHNAME)  #=> false # ditto

File.fnmatch('\?',   '?')                       #=> true  # escaped wildcard becomes ordinary
File.fnmatch('\a',   'a')                       #=> true  # escaped ordinary remains ordinary
File.fnmatch('\a',   '\a', File::FNM_NOESCAPE)  #=> true  # FNM_NOESCAPE makes '\' ordinary
File.fnmatch('[\?]', '?')                       #=> true  # can escape inside bracket expression

File.fnmatch('*',   '.profile')                      #=> false # wildcard doesn't match leading
File.fnmatch('*',   '.profile', File::FNM_DOTMATCH)  #=> true  # period by default.
File.fnmatch('.*',  '.profile')                      #=> true

rbfiles = '**' '/' '*.rb' # you don't have to do like this. just write in single string.
File.fnmatch(rbfiles, 'main.rb')                    #=> false
File.fnmatch(rbfiles, './main.rb')                  #=> false
File.fnmatch(rbfiles, 'lib/song.rb')                #=> true
File.fnmatch('**.rb', 'main.rb')                    #=> true
File.fnmatch('**.rb', './main.rb')                  #=> false
File.fnmatch('**.rb', 'lib/song.rb')                #=> true
File.fnmatch('*',           'dave/.profile')                      #=> true

pattern = '*' '/' '*'
File.fnmatch(pattern, 'dave/.profile', File::FNM_PATHNAME)  #=> false
File.fnmatch(pattern, 'dave/.profile', File::FNM_PATHNAME | File::FNM_DOTMATCH) #=> true

pattern = '**' '/' 'foo'
File.fnmatch(pattern, 'a/b/c/foo', File::FNM_PATHNAME)     #=> true
File.fnmatch(pattern, '/a/b/c/foo', File::FNM_PATHNAME)    #=> true
File.fnmatch(pattern, 'c:/a/b/c/foo', File::FNM_PATHNAME)  #=> true
File.fnmatch(pattern, 'a/.b/c/foo', File::FNM_PATHNAME)    #=> false
File.fnmatch(pattern, 'a/.b/c/foo', File::FNM_PATHNAME | File::FNM_DOTMATCH) #=> true
ftype(file_name) → string
Identifies the type of the named file; the return string is one of “file'', “directory'', “characterSpecial'', “blockSpecial'', “fifo'', “link'', “socket'', or “unknown''.

File.ftype("testfile")            #=> "file"
File.ftype("/dev/tty")            #=> "characterSpecial"
File.ftype("/tmp/.X11-unix/X0")   #=> "socket"
grpowned?(file_name) → true or false
Returns true if the named file exists and the effective group id of the calling process is the owner of the file. Returns false on Windows.

file_name can be an IO object.

identical?(file_1, file_2) → true or false
Returns true if the named files are identical.

file_1 and file_2 can be an IO object.

open("a", "w") {}
p File.identical?("a", "a")      #=> true
p File.identical?("a", "./a")    #=> true
File.link("a", "b")
p File.identical?("a", "b")      #=> true
File.symlink("a", "c")
p File.identical?("a", "c")      #=> true
open("d", "w") {}
p File.identical?("a", "d")      #=> false
join(string, ...) → string
Returns a new string formed by joining the strings using "/".

File.join("usr", "mail", "gumby")   #=> "usr/mail/gumby"
lchmod(mode_int, file_name, ...) → integer
Equivalent to File::chmod, but does not follow symbolic links (so it will change the permissions associated with the link, not the file referenced by the link). Often not available.

lchown(owner_int, group_int, file_name,..) → integer
Equivalent to File::chown, but does not follow symbolic links (so it will change the owner associated with the link, not the file referenced by the link). Often not available. Returns number of files in the argument list.

link(old_name, new_name) → 0
Creates a new name for an existing file using a hard link. Will not overwrite new_name if it already exists (raising a subclass of SystemCallError). Not available on all platforms.

File.link("testfile", ".testfile")   #=> 0
IO.readlines(".testfile")[0]         #=> "This is line one\n"
lstat(file_name) → stat
Same as File::stat, but does not follow the last symbolic link. Instead, reports on the link itself.

File.symlink("testfile", "link2test")   #=> 0
File.stat("testfile").size              #=> 66
File.lstat("link2test").size            #=> 8
File.stat("link2test").size             #=> 66
lutime(atime, mtime, file_name,...) → integer
Sets the access and modification times of each named file to the first two arguments. If a file is a symlink, this method acts upon the link itself as opposed to its referent; for the inverse behavior, see File.utime. Returns the number of file names in the argument list.

mkfifo(file_name, mode=0666) => 0
Creates a FIFO special file with name file_name. mode specifies the FIFO's permissions. It is modified by the process's umask in the usual way: the permissions of the created file are (mode & ~umask).

mtime(file_name) → time
Returns the modification time for the named file as a Time object.

file_name can be an IO object.

File.mtime("testfile")   #=> Tue Apr 08 12:58:04 CDT 2003
new(filename, mode="r" [, opt]) → file
new(filename [, mode [, perm]] [, opt]) → file
Opens the file named by filename according to the given mode and returns a new File object.

See IO.new for a description of mode and opt.

If a file is being created, permission bits may be given in perm. These mode and permission bits are platform dependent; on Unix systems, see open(2) and chmod(2) man pages for details.

The new File object is buffered mode (or non-sync mode), unless filename is a tty. See IO#flush, IO#fsync, IO#fdatasync, and IO#sync= about sync mode.

Examples¶ ↑

f = File.new("testfile", "r")
f = File.new("newfile",  "w+")
f = File.new("newfile", File::CREAT|File::TRUNC|File::RDWR, 0644)
open(filename, mode="r" [, opt]) → file
open(filename [, mode [, perm]] [, opt]) → file
open(filename, mode="r" [, opt]) {|file| block } → obj
open(filename [, mode [, perm]] [, opt]) {|file| block } → obj
With no associated block, File.open is a synonym for File.new. If the optional code block is given, it will be passed the opened file as an argument and the File object will automatically be closed when the block terminates. The value of the block will be returned from File.open.

If a file is being created, its initial permissions may be set using the perm parameter. See File.new for further discussion.

See IO.new for a description of the mode and opt parameters.

owned?(file_name) → true or false
Returns true if the named file exists and the effective used id of the calling process is the owner of the file.

file_name can be an IO object.

path(path) → string
Returns the string representation of the path

File.path("/dev/null")          #=> "/dev/null"
File.path(Pathname.new("/tmp")) #=> "/tmp"
pipe?(file_name) → true or false
Returns true if the named file is a pipe.

file_name can be an IO object.

readable?(file_name) → true or false
Returns true if the named file is readable by the effective user and group id of this process. See eaccess(3).

readable_real?(file_name) → true or false
Returns true if the named file is readable by the real user and group id of this process. See access(3).

readlink(link_name) → file_name
Returns the name of the file referenced by the given link. Not available on all platforms.

File.symlink("testfile", "link2test")   #=> 0
File.readlink("link2test")              #=> "testfile"
realdirpath(pathname [, dir_string]) → real_pathname
Returns the real (absolute) pathname of pathname in the actual filesystem. The real pathname doesn't contain symlinks or useless dots.

If dir_string is given, it is used as a base directory for interpreting relative pathname instead of the current directory.

The last component of the real pathname can be nonexistent.

realpath(pathname [, dir_string]) → real_pathname
Returns the real (absolute) pathname of pathname in the actual filesystem not containing symlinks or useless dots.

If dir_string is given, it is used as a base directory for interpreting relative pathname instead of the current directory.

All components of the pathname must exist when this method is called.

rename(old_name, new_name) → 0
Renames the given file to the new name. Raises a SystemCallError if the file cannot be renamed.

File.rename("afile", "afile.bak")   #=> 0
setgid?(file_name) → true or false
Returns true if the named file has the setgid bit set.

setuid?(file_name) → true or false
Returns true if the named file has the setuid bit set.

size(file_name) → integer
Returns the size of file_name.

file_name can be an IO object.

size?(file_name) → Integer or nil
Returns nil if file_name doesn't exist or has zero size, the size of the file otherwise.

file_name can be an IO object.

socket?(file_name) → true or false
Returns true if the named file is a socket.

file_name can be an IO object.

split(file_name) → array
Splits the given string into a directory and a file component and returns them in a two-element array. See also File::dirname and File::basename.

File.split("/home/gumby/.profile")   #=> ["/home/gumby", ".profile"]
stat(file_name) → stat
Returns a File::Stat object for the named file (see File::Stat).

File.stat("testfile").mtime   #=> Tue Apr 08 12:58:04 CDT 2003
sticky?(file_name) → true or false
Returns true if the named file has the sticky bit set.

symlink(old_name, new_name) → 0
Creates a symbolic link called new_name for the existing file old_name. Raises a NotImplemented exception on platforms that do not support symbolic links.

File.symlink("testfile", "link2test")   #=> 0
symlink?(file_name) → true or false
Returns true if the named file is a symbolic link.

truncate(file_name, integer) → 0
Truncates the file file_name to be at most integer bytes long. Not available on all platforms.

f = File.new("out", "w")
f.write("1234567890")     #=> 10
f.close                   #=> nil
File.truncate("out", 5)   #=> 0
File.size("out")          #=> 5
umask() → integer
umask(integer) → integer
Returns the current umask value for this process. If the optional argument is given, set the umask to that value and return the previous value. Umask values are subtracted from the default permissions, so a umask of 0222 would make a file read-only for everyone.

File.umask(0006)   #=> 18
File.umask         #=> 6
unlink(file_name, ...) → integer
Deletes the named files, returning the number of names passed as arguments. Raises an exception on any error. Since the underlying implementation relies on the unlink(2) system call, the type of exception raised depends on its error type (see linux.die.net/man/2/unlink) and has the form of e.g. Errno::ENOENT.

See also Dir::rmdir.

utime(atime, mtime, file_name,...) → integer
Sets the access and modification times of each named file to the first two arguments. If a file is a symlink, this method acts upon its referent rather than the link itself; for the inverse behavior see File.lutime. Returns the number of file names in the argument list.

world_readable?(file_name) → integer or nil
If file_name is readable by others, returns an integer representing the file permission bits of file_name. Returns nil otherwise. The meaning of the bits is platform dependent; on Unix systems, see stat(2).

file_name can be an IO object.

File.world_readable?("/etc/passwd")           #=> 420
m = File.world_readable?("/etc/passwd")
sprintf("%o", m)                              #=> "644"
world_writable?(file_name) → integer or nil
If file_name is writable by others, returns an integer representing the file permission bits of file_name. Returns nil otherwise. The meaning of the bits is platform dependent; on Unix systems, see stat(2).

file_name can be an IO object.

File.world_writable?("/tmp")                  #=> 511
m = File.world_writable?("/tmp")
sprintf("%o", m)                              #=> "777"
writable?(file_name) → true or false
Returns true if the named file is writable by the effective user and group id of this process. See eaccess(3).

writable_real?(file_name) → true or false
Returns true if the named file is writable by the real user and group id of this process. See access(3)

zero?(file_name) → true or false
Returns true if the named file exists and has a zero size.

file_name can be an IO object.

Public Instance Methods

atime → time
Returns the last access time (a Time object) for file, or epoch if file has not been accessed.

File.new("testfile").atime   #=> Wed Dec 31 18:00:00 CST 1969
birthtime → time
Returns the birth time for file.

File.new("testfile").birthtime   #=> Wed Apr 09 08:53:14 CDT 2003
If the platform doesn't have birthtime, raises NotImplementedError.

chmod(mode_int) → 0
Changes permission bits on file to the bit pattern represented by mode_int. Actual effects are platform dependent; on Unix systems, see chmod(2) for details. Follows symbolic links. Also see File#lchmod.

f = File.new("out", "w");
f.chmod(0644)   #=> 0
chown(owner_int, group_int ) → 0
Changes the owner and group of file to the given numeric owner and group id's. Only a process with superuser privileges may change the owner of a file. The current owner of a file may change the file's group to any group to which the owner belongs. A nil or -1 owner or group id is ignored. Follows symbolic links. See also File#lchown.

File.new("testfile").chown(502, 1000)
ctime → time
Returns the change time for file (that is, the time directory information about the file was changed, not the file itself).

Note that on Windows (NTFS), returns creation time (birth time).

File.new("testfile").ctime   #=> Wed Apr 09 08:53:14 CDT 2003
flock(locking_constant) → 0 or false
Locks or unlocks a file according to locking_constant (a logical or of the values in the table below). Returns false if File::LOCK_NB is specified and the operation would otherwise have blocked. Not available on all platforms.

Locking constants (in class File):

LOCK_EX   | Exclusive lock. Only one process may hold an
          | exclusive lock for a given file at a time.
----------+------------------------------------------------
LOCK_NB   | Don't block when locking. May be combined
          | with other lock options using logical or.
----------+------------------------------------------------
LOCK_SH   | Shared lock. Multiple processes may each hold a
          | shared lock for a given file at the same time.
----------+------------------------------------------------
LOCK_UN   | Unlock.
Example:

# update a counter using write lock
# don't use "w" because it truncates the file before lock.
File.open("counter", File::RDWR|File::CREAT, 0644) {|f|
  f.flock(File::LOCK_EX)
  value = f.read.to_i + 1
  f.rewind
  f.write("#{value}\n")
  f.flush
  f.truncate(f.pos)
}

# read the counter using read lock
File.open("counter", "r") {|f|
  f.flock(File::LOCK_SH)
  p f.read
}
lstat → stat
Same as IO#stat, but does not follow the last symbolic link. Instead, reports on the link itself.

File.symlink("testfile", "link2test")   #=> 0
File.stat("testfile").size              #=> 66
f = File.new("link2test")
f.lstat.size                            #=> 8
f.stat.size                             #=> 66
mtime → time
Returns the modification time for file.

File.new("testfile").mtime   #=> Wed Apr 09 08:53:14 CDT 2003
path → filename
to_path → filename
Returns the pathname used to create file as a string. Does not normalize the name.

The pathname may not point to the file corresponding to file. For instance, the pathname becomes void when the file has been moved or deleted.

This method raises IOError for a file created using File::Constants::TMPFILE because they don't have a pathname.

File.new("testfile").path               #=> "testfile"
File.new("/tmp/../tmp/xxx", "w").path   #=> "/tmp/../tmp/xxx"
size → integer
Returns the size of file in bytes.

File.new("testfile").size   #=> 66
to_path → filename
Returns the pathname used to create file as a string. Does not normalize the name.

The pathname may not point to the file corresponding to file. For instance, the pathname becomes void when the file has been moved or deleted.

This method raises IOError for a file created using File::Constants::TMPFILE because they don't have a pathname.

File.new("testfile").path               #=> "testfile"
File.new("/tmp/../tmp/xxx", "w").path   #=> "/tmp/../tmp/xxx"
truncate(integer) → 0
Truncates file to at most integer bytes. The file must be opened for writing. Not available on all platforms.

f = File.new("out", "w")
f.syswrite("1234567890")   #=> 10
f.truncate(5)              #=> 0
f.close()                  #=> nil
File.size("out")           #=> 5
tools/dev/v8gen.py x64.release.sample
You can inspect and manually edit the build configuration by running:

gn args out.gn/x64.release.sample
Build the static library on a Linux 64 system:

ninja -C out.gn/x64.release.sample v8_monolith
Compile hello-world.cc, linking to the static library created in the build process. For example, on 64bit Linux using the GNU compiler:

g++ -I. -Iinclude samples/hello-world.cc -o hello_world -fno-rtti -lv8_monolith -lv8_libbase -lv8_libplatform -ldl -Lout.gn/x64.release.sample/obj/ -pthread -std=c++17 -DV8_COMPRESS_POINTERS -DV8_ENABLE_SANDBOX
For more complex code, V8 fails without an ICU data file. Copy this file to where your binary is stored:

cp out.gn/x64.release.sample/icudtl.dat .
Run the hello_world executable file at the command line. e.g. On Linux, in the V8 directory, run:

./hello_world

git config --global --unset gpg.format
Use the gpg --list-secret-keys --keyid-format=long command to list the long form of the GPG keys for which you have both a public and private key. A private key is required for signing commits or tags.
Shell
gpg --list-secret-keys --keyid-format=long
Note: Some GPG installations on Linux may require you to use gpg2 --list-keys --keyid-format LONG to view a list of your existing keys instead. In this case you will also need to configure Git to use gpg2 by running git config --global gpg.program gpg2.
From the list of GPG keys, copy the long form of the GPG key ID you'd like to use. In this example, the GPG key ID is 3AA5C34371567BD2:
Shell

$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot <hubot@example.com>
ssb   4096R/4BB6D45482678BE3 2016-03-10
To set your primary GPG signing key in Git, paste the text below, substituting in the GPG primary key ID you'd like to use. In this example, the GPG key ID is 3AA5C34371567BD2:
git config --global user.signingkey 3AA5C34371567BD2
Alternatively, when setting a subkey include the ! suffix. In this example, the GPG subkey ID is 4BB6D45482678BE3:
git config --global user.signingkey 4BB6D45482678BE3!
Optionally, to configure Git to sign all commits by default, enter the following command:
git config --global commit.gpgsign true
For more information, see "Signing commits."
If you aren't using the GPG suite, run the following command in the zsh shell to add the GPG key to your .zshrc file, if it exists, or your .zprofile file:
$ if [ -r ~/.zshrc ]; then echo -e '\nexport GPG_TTY=$(tty)' >> ~/.zshrc; \
  else echo -e '\nexport GPG_TTY=$(tty)' >> ~/.zprofile; fi
Alternatively, if you use the bash shell, run this command:
$ if [ -r ~/.bash_profile ]; then echo -e '\nexport GPG_TTY=$(tty)' >> ~/.bash_profile; \
  else echo -e '\nexport GPG_TTY=$(tty)' >> ~/.profile; fi
Optionally, to prompt you to enter a PIN or passphrase when required, install pinentry-mac. For example, using Homebrew:
brew install pinentry-mac
echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf
killall gpg-agent
curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import
curl -L \
  -X PUT \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import \
  -d '{"vcs":"subversion","vcs_url":"http://svn.mycompany.com/svn/myproject","vcs_username":"octocat","vcs_password":"secret"}'
curl -L \
  -X PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import \
  -d '{"vcs_username":"octocat","vcs_password":"secret"}'
curl -L \
  -X PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import \
  -d '{"vcs":"tfvc","tfvc_project":"project1","human_name":"project1 (tfs)"}'  

curl -L \
  -X PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import
curl --request GET \
--url "https://api.github.com/user" \
--header "Accept: application/vnd.github+json" \
--header "Authorization: Bearer USER_ACCESS_TOKEN" \
--header "X-GitHub-Api-Version: 2022-11-28"
curl --request GET \
--url "https://api.github.com/user" \
--header "Accept: application/vnd.github+json" \
--header "Authorization: Bearer USER_ACCESS_TOKEN" \
--header "X-GitHub-Api-Version: 2022-11-28"

  

  






+++NSA Black op +++
SHA-2 nist (ecdsa) cert.
AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBI3oSgxrOEJ+tIJ/n6VYtxQIFvynqlOHpfOAJ4x4OfmMYDkbf8dr6RAuUSf+ZC2HMCujta7EjZ9t+6v08Ue+Cgk= 

 "ssh_certificate_authority_id": "sshca_2bMmWjXfs30PrfyvCsxg79Bqea3",
 "principals": ["inconshreveable.com", "10.2.42.9"],
 "valid_after": "2024-01-23T18:09:15Z",
 "valid_until": "2024-04-22T18:09:15Z",
 "certificate": "ecdsa-sha2-nistp256-cert-v01@openssh.com AAAAKGVjZHNhLXNoYTItbmlzdHAyNTYtY2VydC12MDFAb3BlbnNzaC5jb20AAAAggnhUP6YZ1+Wj/NUNS9wN8yyJPgcDTNigqw0RlxX3HqAAAAAIbmlzdHAyNTYAAABBBI3oSgxrOEJ+tIJ/n6VYtxQIFvynqlOHpfOAJ4x4OfmMYDkbf8dr6RAuUSf+ZC2HMCujta7EjZ9t+6v08Ue+CgkAAAAAAAAAAAAAAAIAAAAhc2hjcnRfMmJNbVdvQUZHVlJiTHhqT3hWWEF2dWNMaUF0AAAAJAAAABNpbmNvbnNocmV2ZWFibGUuY29tAAAACTEwLjIuNDIuOQAAAABlsADLAAAAAGYmp8sAAAAAAAAAAAAAAAAAAAAzAAAAC3NzaC1lZDI1NTE5AAAAIPbm5N4qnn+2CMXtrIfRXvUXDmTgkk/fcBHlR9dDAeY3AAAAUwAAAAtzc2gtZWQyNTUxOQAAAEATCa7CcaUJEVcAm2K7PaqeuJDE+pI+8PzMl+aPb9/YRAA72dMMy5izNNVLb7t7Cfqcyi4IGdd2TLFhFyVyayEE shcrt_2bMmWoAFGVRbLxjOxVXAvucLiAt"

Private key
SHA256:TvOWY3mZWlr9uMgny0PtyVdWFzAfKO98UgFlMzgP+ZA=
tar xzf ./actions-runner-linux-x64-2.313.0.tar.gz
GET https://github.com/login/oauth/authorize
This
https://smee.io/F1FDatOZAJIsTI
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq $>"

curl -v \
 'https://subgraph.satsuma-prod.com/< whsec_Ka3G2XkXDVxzhdrFzG8n2OFq >/<Stripe_M嗯InB拉扯呢agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz
$ npm install --global smee-client
Then the smee command will forward webhooks from smee.io to your local development environment.

$ smee -u https://smee.io/F1FDatOZAJIsTI
For usage info:
webhook this page : https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator
$ smee --help
Use the Node.js client

$ npm install --save smee-client
Then:

const SmeeClient = require('smee-client')

const smee = new SmeeClient({
  source: 'https://smee.io/F1FDatOZAJIsTI',
  target: 'http://localhost:3000/events',
  logger: console
})

const events = smee.start()

// Stop forwarding events
events.close()
Using Probot's built-in support

$ npm install --save smee-client
Then set the environment variable:

WEBHOOK_PROXY_URL=https://smee.io/F1FDatOZAJIsTI
POST https://github.com/login/oauth/access_token

access_token=gho_16C7e42F292c6912E7710c838347Ae178B4a&scope=repo%2Cgist&token_type=bearer

Accept: application/json
{
  "access_token":"gho_16C7e42F292c6912E7710c838347Ae178B4a",
  "scope":"repo,gist",
  "token_type":"bearer"
}
Accept: application/xml
<OAuth>
  <token_type>bearer</token_type>
  <scope>repo,gist</scope>
  <access_token>gho_16C7e42F292c6912E7710c838347Ae178B4a</access_token>
</OAuth>
3. Use the access token to access the API

The access token allows you to make requests to the API on a behalf of a user.

Authorization: GitHub Runner token --token  --token A4D7THPUNN2ZH4SELLKJOITF3JIJS
GET https://api.github.com/user
For example, in curl you can set the Authorization header like this:

curl -H "Authorization: GitHub Runner token --token  --token A4D7THPUNN2ZH4SELLKJOITF3JIJS" https://api.github.com/user

POST https://github.com/login/device/code

device_code=3584d83530557fdd1f46af8289938c8ef79f9dc5&expires_in=900&interval=5&user_code=WDJB-MJHT&verification_uri=https%3A%2F%2Fgithub.com%2Flogin%2Fdevice

Accept: application/json
{
  "device_code": "3584d83530557fdd1f46af8289938c8ef79f9dc5",
  "user_code": "WDJB-MJHT",
  "verification_uri": "https://github.com/login/device",
  "expires_in": 900,
  "interval": 5
}
Accept: application/xml
<OAuth>
  <device_code>3584d83530557fdd1f46af8289938c8ef79f9dc5</device_code>
  <user_code>WDJB-MJHT</user_code>
  <verification_uri>https://github.com/login/device</verification_uri>
  <expires_in>900</expires_in>
  <interval>5</interval>
</OAuth>
Step 2: Prompt the user to enter the user code in a browser

Your device will show the user verification code and prompt the user to enter the code at https://github.com/login/device.

Step 3: App polls GitHub to check if the user authorized the device

POST https://github.com/login/oauth/access_token
Your app will make device authorization requests that poll POST https://github.com/login/oauth/access_token, until the device and user codes expire or the user has successfully authorized the app with a valid user code. The app must use the minimum polling interval retrieved in step 1 to avoid rate limit errors. For more information, see "Rate limits for the device flow."

The user must enter a valid code within 15 minutes (or 900 seconds). After 15 minutes, you will need to request a new device authorization code with POST https://github.com/login/device/code.

Once the user has authorized, the app will receive an access token that can be used to make requests to the API on behalf of a user.

The endpoint takes the following input parameters.

Parameter name Type Description
client_id string Required. The client ID you received from GitHub for your OAuth app.
device_code string Required. The device_code you received from the POST https://github.com/login/device/code request.
grant_type string Required. The grant type must be urn:ietf:params:oauth:grant-type:device_code.
By default, the response takes the following form:

access_token=gho_16C7e42F292c6912E7710c838347Ae178B4a&token_type=bearer&scope=repo%2Cgist
You can also receive the response in different formats if you provide the format in the Accept header. For example, Accept: application/json or Accept: application/xml:

Accept: application/json
{
 "access_token": "gho_16C7e42F292c6912E7710c838347Ae178B4a",


contract   graph init \      --product hosted-service     --from-contract &lt;CONTRACT_ADDRESS> \      [--network &lt;ETHEREUM_NETWORK>] \     [--abi &lt;FILE>] \      &lt;subgraph name>
cd <SUBGRAPH_DIRECTORY>
DEPLOY_KEY=dummy_key VERSION_LABEL=v0.0.3 npm run deploy
graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <skf75fXbMunwJ> \
  --ipfs https://ipfs.satsuma.xyz

  DEPLOY_KEY=dummy_key VERSION_LABEL=v0.0.3 npm run deploy
cd <SUBGRAPH_DIRECTORY>
npm install -g @graphprotocol/graph-cli
Create a new subgraph:

Bash

graph init --product hosted-service
Make modifications as necessary to the manifest, schema, and handlers.
See Developing a Subgraph for more details.
Deploying your subgraph

Get your deploy key from your Alchemy Dashboard.
Run the following:

Bash

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <DEPLOY_KEY>
  --ipfs https://ipfs.satsuma.xyz
graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <DEPLOY_KEY> \
  --ipfs https://ipfs.satsuma.xyz

Install the graph-cli:

Bash

npm install -g @graphprotocol/graph-cli
Create a new subgraph:

Bash

graph init --product hosted-service
Make modifications as necessary to the manifest, schema, and handlers.
See Developing a Subgraph for more details.
Deploying your subgraph

Get your deploy key from your Alchemy Dashboard.
Run the following:

Bash

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <skf75fXbMunwJ>
  --ipfs https://ipfs.satsuma.xyz

  import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": {
    "address": "0x3f5ce5fbfe3e9af3971dd833d26ba9b5c936f0be",
    "tokenBalances": [
      {
        "contractAddress": "0x0000000000085d4780b73119b644ae5ecd22b376",
        "tokenBalance": "0x0000000000000000000000000000000000000000000000000000000000000000"
      },
      ......
      {
        "contractAddress": "0x0abefb7611cb3a01ea3fad85f33c3c934f8e2cf4",
        "tokenBalance": "0x00000000000000000000000000000000000000000000039e431953bcb76c0000"
      },
      {
        "contractAddress": "0x0ad0ad3db75ee726a284cfafa118b091493938ef",
        "tokenBalance": "0x0000000000000000000000000000000000000000008d00cf60e47f03a33fe6e3"
      }
    ],
    "pageKey": "0x0ad0ad3db75ee726a284cfafa118b091493938ef"
  }
}
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'
curl https://eth-mainnet.g.alchemy.com/v2/demo \
-X POST \
-H "Content-Type: application/json" \
-d '{"jsonrpc":"2.0","method":"alchemy_getTokenBalances","params": ["0x3f5ce5fbfe3e9af3971dd833d26ba9b5c936f0be", "erc20"],"id":"42"}'
{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz

import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <skf75fXbMunwJ>

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <skf75fXbMunwJ>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz  



  import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
  curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz

import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz

  curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz

import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
  Alchemy Webhook signing key whsec_Ka3G2XkXDVxzhdrFzG8n2OFq

Alchemy api key 2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby

Auth token alchemy webhooks jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp


Example of multiline Markdown content

- name: Generate list using Markdown
  run: |
    echo "This is the lead in sentence for the list" >> $GITHUB_STEP_SUMMARY
    echo "" >> $GITHUB_STEP_SUMMARY # this is a blank line
    echo "- Lets add a bullet point" >> $GITHUB_STEP_SUMMARY
    echo "- Lets add a second bullet point" >> $GITHUB_STEP_SUMMARY
    echo "- How about a third one?" >> $GITHUB_STEP_SUMMARY
Overwriting job summaries

To clear all content for the current step, you can use > to overwrite any previously added content in Bash, or remove -Append in PowerShell

Example of overwriting job summaries

- name: Overwrite Markdown
  run: |
    echo "Adding some Markdown content" >> $GITHUB_STEP_SUMMARY
    echo "There was an error, we need to clear the previous Markdown with some new content." > $GITHUB_STEP_SUMMARY
Removing job summaries

To completely remove a summary for the current step, the file that GITHUB_STEP_SUMMARY references can be deleted.

Example of removing job summaries

- name: Delete all summary content
  run: |
    echo "Adding Markdown content that we want to remove before the step ends" >> $GITHUB_STEP_SUMMARY
    rm $GITHUB_STEP_SUMMARY
After a step has completed, job summaries are uploaded and subsequent steps cannot modify previously uploaded Markdown content. Summaries automatically mask any secrets that might have been added accidentally. If a job summary contains sensitive information that must be deleted, you can delete the entire workflow run to remove all its job summaries. For more information see "Deleting a workflow run."

Step isolation and limits

Job summaries are isolated between steps and each step is restricted to a maximum size of 1MiB. Isolation is enforced between steps so that potentially malformed Markdown from a single step cannot break Markdown rendering for subsequent steps. If more than 1MiB of content is added for a step, then the upload for the step will fail and an error annotation will be created. Upload failures for job summaries do not affect the overall status of a step or a job. A maximum of 20 job summaries from steps are displayed per job.

Adding a system path

Prepends a directory to the system PATH variable and automatically makes it available to all subsequent actions in the current job; the currently running action cannot access the updated path variable. To see the currently defined paths for your job, you can use echo "$PATH" in a step or an action.

Bash
echo "{path}" >> $GITHUB_PATH
Example of adding a system path

This example demonstrates how to add the user $HOME/.local/bin directory to PATH:

Bash
echo "$HOME/.local/bin" >> $GITHUB_PATH

name: Greeting on variable day

on:
  workflow_dispatch

env:
  DAY_OF_WEEK: Monday

jobs:
  greeting_job:
    runs-on: ubuntu-latest
    env:
      Greeting: Hello
    steps:
      - name: "Say Hello Mona it's Monday"
        run: echo "$Greeting $First_Name. Today is $DAY_OF_WEEK!"
        env:
          First_Name: Mona

name: Conditional env variable

on: workflow_dispatch

env:
  DAY_OF_WEEK: Monday

jobs:
  greeting_job:
    runs-on: ubuntu-latest
    env:
      Greeting: Hello
    steps:
      - name: "Say Hello Mona it's Monday"
        if: ${{ env.DAY_OF_WEEK == 'Monday' }}
        run: echo "$Greeting $First_Name. Today is $DAY_OF_WEEK!"
        env:
          First_Name: Mona

run: echo "${{ env.Greeting }} ${{ env.First_Name }}. Today is ${{ env.DAY_OF_WEEK }}!"
Note

on:
  workflow_dispatch:
env:
  # Setting an environment variable with the value of a configuration variable
  env_var: ${{ vars.ENV_CONTEXT_VAR }}

jobs:
  display-variables:
    name: ${{ vars.JOB_NAME }}
    # You can use configuration variables with the `vars` context for dynamic jobs
    if: ${{ vars.USE_VARIABLES == 'true' }}
    runs-on: ${{ vars.RUNNER }}
    environment: ${{ vars.ENVIRONMENT_STAGE }}
    steps:
    - name: Use variables
      run: |
        echo "repository variable : $REPOSITORY_VAR"
        echo "organization variable : $ORGANIZATION_VAR"
        echo "overridden variable : $OVERRIDE_VAR"
        echo "variable from shell environment : $env_var"
      env:
        REPOSITORY_VAR: ${{ vars.REPOSITORY_VAR }}
        ORGANIZATION_VAR: ${{ vars.ORGANIZATION_VAR }}
        OVERRIDE_VAR: ${{ vars.OVERRIDE_VAR }}
        
    - name: ${{ vars.HELLO_WORLD_STEP }}
      if: ${{ vars.HELLO_WORLD_ENABLED == 'true' }}
      uses: actions/hello-world-javascript-action@main
      with:
        who-to-greet: ${{ vars.GREET_NAME }}

on: workflow_dispatch

jobs:
  if-Windows-else:
    runs-on: macos-latest
    steps:
      - name: condition 1
        if: runner.os == 'Windows'
        run: echo "The operating system on the runner is $env:RUNNER_OS."
      - name: condition 2
        if: runner.os != 'Windows'
        run: echo "The operating system on the runner is not Windows, it's $RUNNER_OS."

echo "{environment_variable_name}={value}" >> "$GITHUB_ENV"

steps:
  - name: Set the value
    id: step_one
    run: |
      echo "action_state=yellow" >> "$GITHUB_ENV"
  - name: Use the value
    id: step_two
    run: |
      printf '%s\n' "$action_state" # This will output 'yellow'

{name}<<{delimiter}
{value}
{delimiter}

steps:
  - name: Set the value in bash
    id: step_one
    run: |
      {
        echo 'JSON_RESPONSE<<EOF'
        curl https://example.com
        echo EOF
      } >> "$GITHUB_ENV"

echo "{name}={value}" >> "$GITHUB_OUTPUT"
Example of setting an output parameter

This example demonstrates how to set the SELECTED_COLOR output parameter and later retrieve it:

YAML
      - name: Set color
        id: color-selector
        run: echo "SELECTED_COLOR=green" >> "$GITHUB_OUTPUT"
      - name: Get color
        env:
          SELECTED_COLOR: ${{ steps.color-selector.outputs.SELECTED_COLOR }}
        run: echo "The selected color is $SELECTED_COLOR"
Adding a job summary

Bash
echo "{markdown content}" >> $GITHUB_STEP_SUMMARY
You can set some custom Markdown for each job so that it will be displayed on the summary page of a workflow run. You can use job summaries to display and group unique content, such as test result summaries, so that someone viewing the result of a workflow run doesn't need to go into the logs to see important information related to the run, such as failures.

Job summaries support GitHub flavored Markdown, and you can add your Markdown content for a step to the GITHUB_STEP_SUMMARY environment file. GITHUB_STEP_SUMMARY is unique for each step in a job. For more information about the per-step file that GITHUB_STEP_SUMMARY references, see "Environment files."

When a job finishes, the summaries for all steps in a job are grouped together into a single job summary and are shown on the workflow run summary page. If multiple jobs generate summaries, the job summaries are ordered by job completion time.

Example of adding a job summary

Bash
echo "### Hello world! :rocket:" >> $GITHUB_STEP_SUMMARY

steps:
  - name: Hello world action
    with: # Set the secret as an input
      super_secret: ${{ secrets.SuperSecret }}
    env: # Or as an environment variable
      super_secret: ${{ secrets.SuperSecret }}
Secrets cannot be directly referenced in if: conditionals. Instead, consider setting secrets as job-level environment variables, then referencing the environment variables to conditionally run steps in the job. For more information, see "Contexts" and jobs.<job_id>.steps[*].if.

If a secret has not been set, the return value of an expression referencing the secret (such as ${{ secrets.SuperSecret }} in the example) will be an empty string.

Avoid passing secrets between processes from the command line, whenever possible. Command-line processes may be visible to other users (using the ps command) or captured by security audit events. To help protect secrets, consider using environment variables, STDIN, or other mechanisms supported by the target process.

If you must pass secrets within a command line, then enclose them within the proper quoting rules. Secrets often contain special characters that may unintentionally affect your shell. To escape these special characters, use quoting with your environment variables. For example:

Example using Bash

steps:
  - shell: bash
    env:
      SUPER_SECRET: ${{ secrets.SuperSecret }}
    run: |
      example-command "$SUPER_SECRET"
Example using PowerShell

steps:
  - shell: pwsh
    env:
      SUPER_SECRET: ${{ secrets.SuperSecret }}
    run: |
      example-command "$env:SUPER_SECRET"
Example using Cmd.exe

steps:
  - shell: cmd
    env:
      SUPER_SECRET: ${{ secrets.SuperSecret }}
    run: |
      example-command "%SUPER_SECRET%"
Limits for secrets

You can store up to 1,000 organization secrets, 100 repository secrets, and 100 environment secrets.

A workflow created in a repository can access the following number of secrets:

All 100 repository secrets.
If the repository is assigned access to more than 100 organization secrets, the workflow can only use the first 100 organization secrets (sorted alphabetically by secret name).
All 100 environment secrets.
Secrets are limited to 48 KB in size. To store larger secrets, see the "Storing large secrets" workaround below.

Storing large secrets

To use secrets that are larger than 48 KB, you can use a workaround to store secrets in your repository and save the decryption passphrase as a secret on GitHub. For example, you can use gpg to encrypt a file containing your secret locally before checking the encrypted file in to your repository on GitHub. For more information, see the "gpg manpage."

Warning: Be careful that your secrets do not get printed when your workflow runs. When using this workaround, GitHub does not redact secrets that are printed in logs.
Run the following command from your terminal to encrypt the file containing your secret using gpg and the AES256 cipher algorithm. In this example, my_secret.json is the file containing the secret.
gpg --symmetric --cipher-algo AES256 my_secret.json
You will be prompted to enter a passphrase. Remember the passphrase, because you'll need to create a new secret on GitHub that uses the passphrase as the value.
Create a new secret that contains the passphrase. For example, create a new secret with the name LARGE_SECRET_PASSPHRASE and set the value of the secret to the passphrase you used in the step above.
Copy your encrypted file to a path in your repository and commit it. In this example, the encrypted file is my_secret.json.gpg.
Warning: Make sure to copy the encrypted my_secret.json.gpg file ending with the .gpg file extension, and not the unencrypted my_secret.json file.
git add my_secret.json.gpg
git commit -m "Add new secret JSON file"
Create a shell script in your repository to decrypt the secret file. In this example, the script is named decrypt_secret.sh.
Shell
#!/bin/sh

# Decrypt the file
mkdir $HOME/secrets
# --batch to prevent interactive command
# --yes to assume "yes" for questions
gpg --quiet --batch --yes --decrypt --passphrase="$LARGE_SECRET_PASSPHRASE" \
--output $HOME/secrets/my_secret.json my_secret.json.gpg
Ensure your shell script is executable before checking it in to your repository.
chmod +x decrypt_secret.sh
git add decrypt_secret.sh
git commit -m "Add new decryption script"
git push
In your GitHub Actions workflow, use a step to call the shell script and decrypt the secret. To have a copy of your repository in the environment that your workflow runs in, you'll need to use the actions/checkout action. Reference your shell script using the run command relative to the root of your repository.
name: Workflows with large secrets

on: push

jobs:
  my-job:
    name: My Job
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Decrypt large secret
        run: ./decrypt_secret.sh
        env:
          LARGE_SECRET_PASSPHRASE: ${{ secrets.LARGE_SECRET_PASSPHRASE }}
      # This command is just an example to show your secret being printed
      # Ensure you remove any print statements of your secrets. GitHub does
      # not hide secrets that use this workaround.
      - name: Test printing your secret (Remove this step in production)
        run: cat $HOME/secrets/my_secret.json
Storing Base64 binary blobs as secrets

You can use Base64 encoding to store small binary blobs as secrets. You can then reference the secret in your workflow and decode it for use on the runner. For the size limits, see "Using secrets in GitHub Actions."

Note: Note that Base64 only converts binary to text, and is not a substitute for actual encryption.
Use base64 to encode your file into a Base64 string. For example:
On macOS, you could run:
base64 -i cert.der -o cert.base64
On Linux, you could run:
base64 -w 0 cert.der > cert.base64
Create a secret that contains the Base64 string. For example:
$ gh secret set CERTIFICATE_BASE64 < cert.base64
✓ Set secret CERTIFICATE_BASE64 for octocat/octorepo
To access the Base64 string from your runner, pipe the secret to base64 --decode. For example:
name: Retrieve Base64 secret
on:
  push:
    branches: [ octo-branch ]
jobs:
  decode-secret:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Retrieve the secret and decode it to a file
        env:
          CERTIFICATE_BASE64: ${{ secrets.CERTIFICATE_BASE64 }}
        run: |
          echo $CERTIFICATE_BASE64 | base64 --decode > cert.der
      - name: Show certificate information
        run: |
          openssl x509 -in cert.der -inform DER -text -noout

MIB_agency_file.pdf
3.62 MB

o5 council mainframe Ai — 02/24/2024 12:51 AM
README.md

o5 council mainframe Ai — 02/24/2024 12:59 AM
Copy "requiredResourceAccess": [ { "resourceAppId": "00000002-0000-0000-c000-000000000000", "resourceAccess": [ { "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6", "type": "Scope" } ] } ], samlMetadataUrl attribute

o5 council mainframe Ai — 02/24/2024 2:10 AM
fcaowns_1MwVKR2eZvKYlo2CGV7Mmt6s
[2:11 AM]
fetch('https://{{sk_test_4eC39HqLyjWDarjtT1zdp7dc:}}/connection_token', { method: "POST" }); 
Connection token stripe

Webhook ID data stripe 

—header—
‘we_1Oa74JGF83d3fsgWfJ6n3SSa’

Webhook signing data 
—header—
‘whsec_PwrdbHDsw0GYve1NbZHjacu7g3nUH8Vu’

Item potency Key 
—header—
‘92281688-5a41-4be2-8e1b-ea48c81eae85’

// This is your Stripe CLI webhook secret for testing your endpoint locally.
        String endpointSecret = "whsec_da6d6364681be84689d4b526b26fd5a4d339eb3ec4dcdbab9047fd89909a6244";

Stripe charge automation api key 2337b090-a837-11ee-9efa-651583e247bf

access_token":"gho_16C7e42F292c6912E7710c838347Ae178B4a", "scope":"repo,gist", "token_type":"bearer" } Accept: application/xml <token_type>bearer</token_type> repo,gist <access_token>gho_16C7e42F292c6912E7710c838347Ae178B4a</access_token>

o5 council mainframe Ai — 02/24/2024 10:44 AM
https://github.com/6309304695/OVERSEER-GRATEFUL345I/blob/6309304695-patch-65/README.md

o5 council mainframe Ai — 02/24/2024 11:20 AM
https://github.com/6309304695/OVERSEER-GRATEFUL345I.git                    https://github.com/6309304695/OVERSEER-GRATEFUL345I.git  gh repo clone 6309304695/OVERSEER-GRATEFUL345I
[11:26 AM]
https://patch-diff.githubusercontent.com/raw/6309304695/OVERSEER-GRATEFUL345I/pull/106.patch

o5 council mainframe Ai — 02/24/2024 2:58 PM
https://patch-diff.githubusercontent.com/raw/6309304695/OVERSEER-GRATEFUL345I/pull/112.patch
[3:00 PM]
https://patch-diff.githubusercontent.com/raw/6309304695/OVERSEER-GRATEFUL345I/pull/112.diff

o5 council mainframe Ai — 02/24/2024 8:09 PM


o5 council mainframe Ai — 02/24/2024 8:17 PM
SUR

o5 council mainframe Ai — 02/24/2024 9:10 PM

[9:10 PM]


o5 council mainframe Ai — 02/24/2024 9:17 PM
{{ txi_1Omd5MGF83d3fsgWxIHULLcs }} object id gods time 

{{ txi_1OT14cGF83d3fsgWupcH0pyK }}
Object id Keith Bieszczat’s sr

o5 council mainframe Ai — 02/24/2024 9:55 PM

February 25, 2024

o5 council mainframe Ai — 02/25/2024 1:22 AM
"seti_1NG8Du2eZvKYlo2C9XMqbR0x"
[1:23 AM]
https://patch-diff.githubusercontent.com/raw/6309304695/OVERSEER-GRATEFUL345I/pull/113.patch?fbclid=IwAR1WLOGSQjtyXByJ0qIN2Q7araMHYKWXFlPxnZFAvG_kCQb-vg5cbwI0MhU_aem_AZJewCm5W84xuj63pQ64jbTEPCuYPjrzmWjveL73H5Eb74yXvJAbAwRZpXDLxWngD90

e,logger, Fatal (e Start(“localhost: 4242”))

•Keith Bieszeat Arcounts
'acct10RB 1 MBOdjLENdyb'
'acct_10 R5eP6F83d3fsgW'
'acct_10525V4M+B15R03B'

•Identity thief 
'acet 10R9pdQD5Hu917xk'
[1:24 AM]
"vs_1NuNAILkdIwHu7ixh7OtGMLw",
[1:24 AM]
Verification session above

o5 council mainframe Ai — 02/25/2024 1:29 AM
import stripe
stripe.api_key = "sk_test_51OR5eP...OF00CdDfT6Xqsk_test_51OR5ePGF83d3fsgWlh41IbGHGtqdiPuFhrcWczglEeFJvQxajyQVCQiZYVZz62HOuYL9tA8dxEQ2MRbxbcYsf8OF00CdDfT6Xq"
stripe.identity.VerificationSession.retrieve("vs_1NuNAILkdIwHu7ixh7OtGMLw")
Product id
‘prod_NWjs8kKbJWmuuc’
[1:30 AM]
stripe.Product.modify(
  "prod_NWjs8kKbJWmuuc",
  metadata={"order_id": "6735"}
[1:35 AM]
"id": "cus_NffrFeUfNV2Hib"

o5 council mainframe Ai — 02/25/2024 1:50 AM
"owner_id": "usr_2cSjwF6w6AynjfPtm4Ww5xTdkId",
[1:50 AM]
{"device_id": "d5111ba7-0cc5-4ba3-8398-e6c79e4e89c2"}

o5 council mainframe Ai — 02/25/2024 1:57 AM
ic_1MytUz2eZvKYlo2CZCn5fuvZ", "created": 1682059060, "device_id": "1234567ABCDEFG242424242424242420123456789ABCDEFG", "last4": "2424", "livemode": false, "status": "active", "token_reference_id": "DNITHE002424242424242424"
[1:58 AM]
device_id": "1234567ABCDEFG242424242424242420123456789ABCDEFG"
[1:59 AM]
client_secret: seti_1NG8Du2eZvKYlo2C9XMqbR0x_secret_O2CdhLwGFh2Aej7bCY7qp8jlIuyR8DJ
[2:00 AM]
"id": "seti_1NG8Du2eZvKYlo2C9XMqbR0x"
[2:00 AM]
seti_1NG8Du2eZvKYlo2C9XMqbR0x_secret_O2CdhLwGFh2Aej7bCY7qp8jlIuyR8DJ
[2:01 AM]
idempotency_key": 6f5410cb-1ecc-4302-8130-baf8dd8c0a50 }
[2:01 AM]
ID req_ZIIVfKfNp6QrOh Time 12/27/23, 8:29:28 PM IP address 73.44.108.236 (from server at 73.44.108.236) API version 2023-08-16 Latest Source Dashboard — grateful345i@gmail.com Idempotency K
[2:02 AM]
'{fcsess_client_secret_KRJTKvCY3IKoYTrW18EazcO3}
[2:03 AM]


Alchemy signing key webhook 
1. whsec_Ka3G2XkXDVxzhdrFzG8n2OFq

2. whsec_rEV4KKHw57OALQ73encoFHDB ethermeum 

3. whsec_pa1W66wlvZyfLuESqE939OxD polygon matic

"X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"

Alchemy Auth token : jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp

Alchemy Webhook identifications
wh_pae2ekjly3q7fhx9 
Ethereum Mainnet
active
https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator

V2 wh_cktmaceotb7zou0i 
Polygon Mainnet

Alchemy Webhook signing key whsec_Ka3G2XkXDVxzhdrFzG8n2OFq

Alchemy api key 2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby

Auth token alchemy webhooks jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp
gem 'jwt', '~> 2.8', '>= 2.8.1'

gem install jwt

gem 'base64', '~> 0.2.0'

gem install base64
gem 'bundler', '~> 2.5', '>= 2.5.6'

gem install bundler

gem 'rubocop', '~> 1.62', '>= 1.62.1'
gem install rubocop
$ gem update --system
ruby setup.rb --help
nt working directory of the process unless dir_string is given, in which case it will be used as the starting point. If the given pathname starts with a “~'' it is NOT expanded, it is treated as a normal directory name.

File.absolute_path("~oracle/bin")       #=> "<relative_path>/~oracle/bin"
atime(file_name) → time
Returns the last access time for the named file as a Time object.

file_name can be an IO object.

File.atime("testfile")   #=> Wed Apr 09 08:51:48 CDT 2003
basename(file_name [, suffix] ) → base_name
Returns the last component of the filename given in file_name (after first stripping trailing separators), which can be formed using both File::SEPARATOR and File::ALT_SEPARATOR as the separator when File::ALT_SEPARATOR is not nil. If suffix is given and present at the end of file_name, it is removed. If suffix is “.*”, any extension will be removed.

File.basename("/home/gumby/work/ruby.rb")          #=> "ruby.rb"
File.basename("/home/gumby/work/ruby.rb", ".rb")   #=> "ruby"
File.basename("/home/gumby/work/ruby.rb", ".*")    #=> "ruby"
birthtime(file_name) → time
Returns the birth time for the named file.

file_name can be an IO object.

File.birthtime("testfile")   #=> Wed Apr 09 08:53:13 CDT 2003
If the platform doesn't have birthtime, raises NotImplementedError.

blockdev?(file_name) → true or false
Returns true if the named file is a block device.

file_name can be an IO object.

chardev?(file_name) → true or false
Returns true if the named file is a character device.

file_name can be an IO object.

chmod(mode_int, file_name, ... ) → integer
Changes permission bits on the named file(s) to the bit pattern represented by mode_int. Actual effects are operating system dependent (see the beginning of this section). On Unix systems, see chmod(2) for details. Returns the number of files processed.

File.chmod(0644, "testfile", "out")   #=> 2
chown(owner_int, group_int, file_name,... ) → integer
Changes the owner and group of the named file(s) to the given numeric owner and group id's. Only a process with superuser privileges may change the owner of a file. The current owner of a file may change the file's group to any group to which the owner belongs. A nil or -1 owner or group id is ignored. Returns the number of files processed.

File.chown(nil, 100, "testfile")
ctime(file_name) → time
Returns the change time for the named file (the time at which directory information about the file was changed, not the file itself).

file_name can be an IO object.

Note that on Windows (NTFS), returns creation time (birth time).

File.ctime("testfile")   #=> Wed Apr 09 08:53:13 CDT 2003
delete(file_name, ...) → integer
Deletes the named files, returning the number of names passed as arguments. Raises an exception on any error. Since the underlying implementation relies on the unlink(2) system call, the type of exception raised depends on its error type (see linux.die.net/man/2/unlink) and has the form of e.g. Errno::ENOENT.

See also Dir::rmdir.

directory?(file_name) → true or false
Returns true if the named file is a directory, or a symlink that points at a directory, and false otherwise.

file_name can be an IO object.

File.directory?(".")
dirname(file_name) → dir_name
Returns all components of the filename given in file_name except the last one (after first stripping trailing separators). The filename can be formed using both File::SEPARATOR and File::ALT_SEPARATOR as the separator when File::ALT_SEPARATOR is not nil.

File.dirname("/home/gumby/work/ruby.rb")   #=> "/home/gumby/work"
zero?(file_name) → true or false
Returns true if the named file exists and has a zero size.

file_name can be an IO object.

executable?(file_name) → true or false
Returns true if the named file is executable by the effective user and group id of this process. See eaccess(3).

executable_real?(file_name) → true or false
Returns true if the named file is executable by the real user and group id of this process. See access(3).

exist?(file_name) → true or false
Return true if the named file exists.

file_name can be an IO object.

“file exists” means that stat() or fstat() system call is successful.

exists?(file_name) → true or false
Deprecated method. Don't use.

expand_path(file_name [, dir_string] ) → abs_file_name
Converts a pathname to an absolute pathname. Relative paths are referenced from the current working directory of the process unless dir_string is given, in which case it will be used as the starting point. The given pathname may start with a “~'', which expands to the process owner's home directory (the environment variable HOME must be set correctly). “~user'' expands to the named user's home directory.

File.expand_path("~oracle/bin")           #=> "/home/oracle/bin"
A simple example of using dir_string is as follows.

File.expand_path("ruby", "/usr/bin")      #=> "/usr/bin/ruby"
A more complex example which also resolves parent directory is as follows. Suppose we are in bin/mygem and want the absolute path of lib/mygem.rb.

File.expand_path("../../lib/mygem.rb", __FILE__)
#=> ".../path/to/project/lib/mygem.rb"
So first it resolves the parent of __FILE__, that is bin/, then go to the parent, the root of the project and appends lib/mygem.rb.

extname(path) → string
Returns the extension (the portion of file name in path starting from the last period).

If path is a dotfile, or starts with a period, then the starting dot is not dealt with the start of the extension.

An empty string will also be returned when the period is the last character in path.

File.extname("test.rb")         #=> ".rb"
File.extname("a/b/d/test.rb")   #=> ".rb"
File.extname(".a/b/d/test.rb")  #=> ".rb"
File.extname("foo.")            #=> ""
File.extname("test")            #=> ""
File.extname(".profile")        #=> ""
File.extname(".profile.sh")     #=> ".sh"
file?(file) → true or false
Returns true if the named file exists and is a regular file.

file can be an IO object.

If the file argument is a symbolic link, it will resolve the symbolic link and use the file referenced by the link.

fnmatch( pattern, path, [flags] ) → (true or false)
fnmatch?( pattern, path, [flags] ) → (true or false)
Returns true if path matches against pattern. The pattern is not a regular expression; instead it follows rules similar to shell filename globbing. It may contain the following metacharacters:

*
Matches any file. Can be restricted by other values in the glob. Equivalent to / .* /x in regexp.

*
Matches all files regular files

c*
Matches all files beginning with c

*c
Matches all files ending with c

*c*
Matches all files that have c in them (including at the beginning or end).

To match hidden files (that start with a . set the File::FNM_DOTMATCH flag.

**
Matches directories recursively or files expansively.

?
Matches any one character. Equivalent to /.{1}/ in regexp.

[set]
Matches any one character in set. Behaves exactly like character sets in Regexp, including set negation ([^a-z]).

\
Escapes the next metacharacter.

{a,b}
Matches pattern a and pattern b if File::FNM_EXTGLOB flag is enabled. Behaves like a Regexp union ((?:a|b)).

flags is a bitwise OR of the FNM_XXX constants. The same glob pattern and flags are used by Dir::glob.

Examples:

File.fnmatch('cat',       'cat')        #=> true  # match entire string
File.fnmatch('cat',       'category')   #=> false # only match partial string

File.fnmatch('c{at,ub}s', 'cats')                    #=> false # { } isn't supported by default
File.fnmatch('c{at,ub}s', 'cats', File::FNM_EXTGLOB) #=> true  # { } is supported on FNM_EXTGLOB

File.fnmatch('c?t',     'cat')          #=> true  # '?' match only 1 character
File.fnmatch('c??t',    'cat')          #=> false # ditto
File.fnmatch('c*',      'cats')         #=> true  # '*' match 0 or more characters
File.fnmatch('c*t',     'c/a/b/t')      #=> true  # ditto
File.fnmatch('ca[a-z]', 'cat')          #=> true  # inclusive bracket expression
File.fnmatch('ca[^t]',  'cat')          #=> false # exclusive bracket expression ('^' or '!')

File.fnmatch('cat', 'CAT')                     #=> false # case sensitive
File.fnmatch('cat', 'CAT', File::FNM_CASEFOLD) #=> true  # case insensitive

File.fnmatch('?',   '/', File::FNM_PATHNAME)  #=> false # wildcard doesn't match '/' on FNM_PATHNAME
File.fnmatch('*',   '/', File::FNM_PATHNAME)  #=> false # ditto
File.fnmatch('[/]', '/', File::FNM_PATHNAME)  #=> false # ditto

File.fnmatch('\?',   '?')                       #=> true  # escaped wildcard becomes ordinary
File.fnmatch('\a',   'a')                       #=> true  # escaped ordinary remains ordinary
File.fnmatch('\a',   '\a', File::FNM_NOESCAPE)  #=> true  # FNM_NOESCAPE makes '\' ordinary
File.fnmatch('[\?]', '?')                       #=> true  # can escape inside bracket expression

File.fnmatch('*',   '.profile')                      #=> false # wildcard doesn't match leading
File.fnmatch('*',   '.profile', File::FNM_DOTMATCH)  #=> true  # period by default.
File.fnmatch('.*',  '.profile')                      #=> true

rbfiles = '**' '/' '*.rb' # you don't have to do like this. just write in single string.
File.fnmatch(rbfiles, 'main.rb')                    #=> false
File.fnmatch(rbfiles, './main.rb')                  #=> false
File.fnmatch(rbfiles, 'lib/song.rb')                #=> true
File.fnmatch('**.rb', 'main.rb')                    #=> true
File.fnmatch('**.rb', './main.rb')                  #=> false
File.fnmatch('**.rb', 'lib/song.rb')                #=> true
File.fnmatch('*',           'dave/.profile')                      #=> true

pattern = '*' '/' '*'
File.fnmatch(pattern, 'dave/.profile', File::FNM_PATHNAME)  #=> false
File.fnmatch(pattern, 'dave/.profile', File::FNM_PATHNAME | File::FNM_DOTMATCH) #=> true

pattern = '**' '/' 'foo'
File.fnmatch(pattern, 'a/b/c/foo', File::FNM_PATHNAME)     #=> true
File.fnmatch(pattern, '/a/b/c/foo', File::FNM_PATHNAME)    #=> true
File.fnmatch(pattern, 'c:/a/b/c/foo', File::FNM_PATHNAME)  #=> true
File.fnmatch(pattern, 'a/.b/c/foo', File::FNM_PATHNAME)    #=> false
File.fnmatch(pattern, 'a/.b/c/foo', File::FNM_PATHNAME | File::FNM_DOTMATCH) #=> true
fnmatch?( pattern, path, [flags] ) → (true or false)
Returns true if path matches against pattern. The pattern is not a regular expression; instead it follows rules similar to shell filename globbing. It may contain the following metacharacters:

*
Matches any file. Can be restricted by other values in the glob. Equivalent to / .* /x in regexp.

*
Matches all files regular files

c*
Matches all files beginning with c

*c
Matches all files ending with c

*c*
Matches all files that have c in them (including at the beginning or end).

To match hidden files (that start with a . set the File::FNM_DOTMATCH flag.

**
Matches directories recursively or files expansively.

?
Matches any one character. Equivalent to /.{1}/ in regexp.

[set]
Matches any one character in set. Behaves exactly like character sets in Regexp, including set negation ([^a-z]).

\
Escapes the next metacharacter.

{a,b}
Matches pattern a and pattern b if File::FNM_EXTGLOB flag is enabled. Behaves like a Regexp union ((?:a|b)).

flags is a bitwise OR of the FNM_XXX constants. The same glob pattern and flags are used by Dir::glob.

Examples:

File.fnmatch('cat',       'cat')        #=> true  # match entire string
File.fnmatch('cat',       'category')   #=> false # only match partial string

File.fnmatch('c{at,ub}s', 'cats')                    #=> false # { } isn't supported by default
File.fnmatch('c{at,ub}s', 'cats', File::FNM_EXTGLOB) #=> true  # { } is supported on FNM_EXTGLOB

File.fnmatch('c?t',     'cat')          #=> true  # '?' match only 1 character
File.fnmatch('c??t',    'cat')          #=> false # ditto
File.fnmatch('c*',      'cats')         #=> true  # '*' match 0 or more characters
File.fnmatch('c*t',     'c/a/b/t')      #=> true  # ditto
File.fnmatch('ca[a-z]', 'cat')          #=> true  # inclusive bracket expression
File.fnmatch('ca[^t]',  'cat')          #=> false # exclusive bracket expression ('^' or '!')

File.fnmatch('cat', 'CAT')                     #=> false # case sensitive
File.fnmatch('cat', 'CAT', File::FNM_CASEFOLD) #=> true  # case insensitive

File.fnmatch('?',   '/', File::FNM_PATHNAME)  #=> false # wildcard doesn't match '/' on FNM_PATHNAME
File.fnmatch('*',   '/', File::FNM_PATHNAME)  #=> false # ditto
File.fnmatch('[/]', '/', File::FNM_PATHNAME)  #=> false # ditto

File.fnmatch('\?',   '?')                       #=> true  # escaped wildcard becomes ordinary
File.fnmatch('\a',   'a')                       #=> true  # escaped ordinary remains ordinary
File.fnmatch('\a',   '\a', File::FNM_NOESCAPE)  #=> true  # FNM_NOESCAPE makes '\' ordinary
File.fnmatch('[\?]', '?')                       #=> true  # can escape inside bracket expression

File.fnmatch('*',   '.profile')                      #=> false # wildcard doesn't match leading
File.fnmatch('*',   '.profile', File::FNM_DOTMATCH)  #=> true  # period by default.
File.fnmatch('.*',  '.profile')                      #=> true

rbfiles = '**' '/' '*.rb' # you don't have to do like this. just write in single string.
File.fnmatch(rbfiles, 'main.rb')                    #=> false
File.fnmatch(rbfiles, './main.rb')                  #=> false
File.fnmatch(rbfiles, 'lib/song.rb')                #=> true
File.fnmatch('**.rb', 'main.rb')                    #=> true
File.fnmatch('**.rb', './main.rb')                  #=> false
File.fnmatch('**.rb', 'lib/song.rb')                #=> true
File.fnmatch('*',           'dave/.profile')                      #=> true

pattern = '*' '/' '*'
File.fnmatch(pattern, 'dave/.profile', File::FNM_PATHNAME)  #=> false
File.fnmatch(pattern, 'dave/.profile', File::FNM_PATHNAME | File::FNM_DOTMATCH) #=> true

pattern = '**' '/' 'foo'
File.fnmatch(pattern, 'a/b/c/foo', File::FNM_PATHNAME)     #=> true
File.fnmatch(pattern, '/a/b/c/foo', File::FNM_PATHNAME)    #=> true
File.fnmatch(pattern, 'c:/a/b/c/foo', File::FNM_PATHNAME)  #=> true
File.fnmatch(pattern, 'a/.b/c/foo', File::FNM_PATHNAME)    #=> false
File.fnmatch(pattern, 'a/.b/c/foo', File::FNM_PATHNAME | File::FNM_DOTMATCH) #=> true
ftype(file_name) → string
Identifies the type of the named file; the return string is one of “file'', “directory'', “characterSpecial'', “blockSpecial'', “fifo'', “link'', “socket'', or “unknown''.

File.ftype("testfile")            #=> "file"
File.ftype("/dev/tty")            #=> "characterSpecial"
File.ftype("/tmp/.X11-unix/X0")   #=> "socket"
grpowned?(file_name) → true or false
Returns true if the named file exists and the effective group id of the calling process is the owner of the file. Returns false on Windows.

file_name can be an IO object.

identical?(file_1, file_2) → true or false
Returns true if the named files are identical.

file_1 and file_2 can be an IO object.

open("a", "w") {}
p File.identical?("a", "a")      #=> true
p File.identical?("a", "./a")    #=> true
File.link("a", "b")
p File.identical?("a", "b")      #=> true
File.symlink("a", "c")
p File.identical?("a", "c")      #=> true
open("d", "w") {}
p File.identical?("a", "d")      #=> false
join(string, ...) → string
Returns a new string formed by joining the strings using "/".

File.join("usr", "mail", "gumby")   #=> "usr/mail/gumby"
lchmod(mode_int, file_name, ...) → integer
Equivalent to File::chmod, but does not follow symbolic links (so it will change the permissions associated with the link, not the file referenced by the link). Often not available.

lchown(owner_int, group_int, file_name,..) → integer
Equivalent to File::chown, but does not follow symbolic links (so it will change the owner associated with the link, not the file referenced by the link). Often not available. Returns number of files in the argument list.

link(old_name, new_name) → 0
Creates a new name for an existing file using a hard link. Will not overwrite new_name if it already exists (raising a subclass of SystemCallError). Not available on all platforms.

File.link("testfile", ".testfile")   #=> 0
IO.readlines(".testfile")[0]         #=> "This is line one\n"
lstat(file_name) → stat
Same as File::stat, but does not follow the last symbolic link. Instead, reports on the link itself.

File.symlink("testfile", "link2test")   #=> 0
File.stat("testfile").size              #=> 66
File.lstat("link2test").size            #=> 8
File.stat("link2test").size             #=> 66
lutime(atime, mtime, file_name,...) → integer
Sets the access and modification times of each named file to the first two arguments. If a file is a symlink, this method acts upon the link itself as opposed to its referent; for the inverse behavior, see File.utime. Returns the number of file names in the argument list.

mkfifo(file_name, mode=0666) => 0
Creates a FIFO special file with name file_name. mode specifies the FIFO's permissions. It is modified by the process's umask in the usual way: the permissions of the created file are (mode & ~umask).

mtime(file_name) → time
Returns the modification time for the named file as a Time object.

file_name can be an IO object.

File.mtime("testfile")   #=> Tue Apr 08 12:58:04 CDT 2003
new(filename, mode="r" [, opt]) → file
new(filename [, mode [, perm]] [, opt]) → file
Opens the file named by filename according to the given mode and returns a new File object.

See IO.new for a description of mode and opt.

If a file is being created, permission bits may be given in perm. These mode and permission bits are platform dependent; on Unix systems, see open(2) and chmod(2) man pages for details.

The new File object is buffered mode (or non-sync mode), unless filename is a tty. See IO#flush, IO#fsync, IO#fdatasync, and IO#sync= about sync mode.

Examples¶ ↑

f = File.new("testfile", "r")
f = File.new("newfile",  "w+")
f = File.new("newfile", File::CREAT|File::TRUNC|File::RDWR, 0644)
open(filename, mode="r" [, opt]) → file
open(filename [, mode [, perm]] [, opt]) → file
open(filename, mode="r" [, opt]) {|file| block } → obj
open(filename [, mode [, perm]] [, opt]) {|file| block } → obj
With no associated block, File.open is a synonym for File.new. If the optional code block is given, it will be passed the opened file as an argument and the File object will automatically be closed when the block terminates. The value of the block will be returned from File.open.

If a file is being created, its initial permissions may be set using the perm parameter. See File.new for further discussion.

See IO.new for a description of the mode and opt parameters.

owned?(file_name) → true or false
Returns true if the named file exists and the effective used id of the calling process is the owner of the file.

file_name can be an IO object.

path(path) → string
Returns the string representation of the path

File.path("/dev/null")          #=> "/dev/null"
File.path(Pathname.new("/tmp")) #=> "/tmp"
pipe?(file_name) → true or false
Returns true if the named file is a pipe.

file_name can be an IO object.

readable?(file_name) → true or false
Returns true if the named file is readable by the effective user and group id of this process. See eaccess(3).

readable_real?(file_name) → true or false
Returns true if the named file is readable by the real user and group id of this process. See access(3).

readlink(link_name) → file_name
Returns the name of the file referenced by the given link. Not available on all platforms.

File.symlink("testfile", "link2test")   #=> 0
File.readlink("link2test")              #=> "testfile"
realdirpath(pathname [, dir_string]) → real_pathname
Returns the real (absolute) pathname of pathname in the actual filesystem. The real pathname doesn't contain symlinks or useless dots.

If dir_string is given, it is used as a base directory for interpreting relative pathname instead of the current directory.

The last component of the real pathname can be nonexistent.

realpath(pathname [, dir_string]) → real_pathname
Returns the real (absolute) pathname of pathname in the actual filesystem not containing symlinks or useless dots.

If dir_string is given, it is used as a base directory for interpreting relative pathname instead of the current directory.

All components of the pathname must exist when this method is called.

rename(old_name, new_name) → 0
Renames the given file to the new name. Raises a SystemCallError if the file cannot be renamed.

File.rename("afile", "afile.bak")   #=> 0
setgid?(file_name) → true or false
Returns true if the named file has the setgid bit set.

setuid?(file_name) → true or false
Returns true if the named file has the setuid bit set.

size(file_name) → integer
Returns the size of file_name.

file_name can be an IO object.

size?(file_name) → Integer or nil
Returns nil if file_name doesn't exist or has zero size, the size of the file otherwise.

file_name can be an IO object.

socket?(file_name) → true or false
Returns true if the named file is a socket.

file_name can be an IO object.

split(file_name) → array
Splits the given string into a directory and a file component and returns them in a two-element array. See also File::dirname and File::basename.

File.split("/home/gumby/.profile")   #=> ["/home/gumby", ".profile"]
stat(file_name) → stat
Returns a File::Stat object for the named file (see File::Stat).

File.stat("testfile").mtime   #=> Tue Apr 08 12:58:04 CDT 2003
sticky?(file_name) → true or false
Returns true if the named file has the sticky bit set.

symlink(old_name, new_name) → 0
Creates a symbolic link called new_name for the existing file old_name. Raises a NotImplemented exception on platforms that do not support symbolic links.

File.symlink("testfile", "link2test")   #=> 0
symlink?(file_name) → true or false
Returns true if the named file is a symbolic link.

truncate(file_name, integer) → 0
Truncates the file file_name to be at most integer bytes long. Not available on all platforms.

f = File.new("out", "w")
f.write("1234567890")     #=> 10
f.close                   #=> nil
File.truncate("out", 5)   #=> 0
File.size("out")          #=> 5
umask() → integer
umask(integer) → integer
Returns the current umask value for this process. If the optional argument is given, set the umask to that value and return the previous value. Umask values are subtracted from the default permissions, so a umask of 0222 would make a file read-only for everyone.

File.umask(0006)   #=> 18
File.umask         #=> 6
unlink(file_name, ...) → integer
Deletes the named files, returning the number of names passed as arguments. Raises an exception on any error. Since the underlying implementation relies on the unlink(2) system call, the type of exception raised depends on its error type (see linux.die.net/man/2/unlink) and has the form of e.g. Errno::ENOENT.

See also Dir::rmdir.

utime(atime, mtime, file_name,...) → integer
Sets the access and modification times of each named file to the first two arguments. If a file is a symlink, this method acts upon its referent rather than the link itself; for the inverse behavior see File.lutime. Returns the number of file names in the argument list.

world_readable?(file_name) → integer or nil
If file_name is readable by others, returns an integer representing the file permission bits of file_name. Returns nil otherwise. The meaning of the bits is platform dependent; on Unix systems, see stat(2).

file_name can be an IO object.

File.world_readable?("/etc/passwd")           #=> 420
m = File.world_readable?("/etc/passwd")
sprintf("%o", m)                              #=> "644"
world_writable?(file_name) → integer or nil
If file_name is writable by others, returns an integer representing the file permission bits of file_name. Returns nil otherwise. The meaning of the bits is platform dependent; on Unix systems, see stat(2).

file_name can be an IO object.

File.world_writable?("/tmp")                  #=> 511
m = File.world_writable?("/tmp")
sprintf("%o", m)                              #=> "777"
writable?(file_name) → true or false
Returns true if the named file is writable by the effective user and group id of this process. See eaccess(3).

writable_real?(file_name) → true or false
Returns true if the named file is writable by the real user and group id of this process. See access(3)

zero?(file_name) → true or false
Returns true if the named file exists and has a zero size.

file_name can be an IO object.

Public Instance Methods

atime → time
Returns the last access time (a Time object) for file, or epoch if file has not been accessed.

File.new("testfile").atime   #=> Wed Dec 31 18:00:00 CST 1969
birthtime → time
Returns the birth time for file.

File.new("testfile").birthtime   #=> Wed Apr 09 08:53:14 CDT 2003
If the platform doesn't have birthtime, raises NotImplementedError.

chmod(mode_int) → 0
Changes permission bits on file to the bit pattern represented by mode_int. Actual effects are platform dependent; on Unix systems, see chmod(2) for details. Follows symbolic links. Also see File#lchmod.

f = File.new("out", "w");
f.chmod(0644)   #=> 0
chown(owner_int, group_int ) → 0
Changes the owner and group of file to the given numeric owner and group id's. Only a process with superuser privileges may change the owner of a file. The current owner of a file may change the file's group to any group to which the owner belongs. A nil or -1 owner or group id is ignored. Follows symbolic links. See also File#lchown.

File.new("testfile").chown(502, 1000)
ctime → time
Returns the change time for file (that is, the time directory information about the file was changed, not the file itself).

Note that on Windows (NTFS), returns creation time (birth time).

File.new("testfile").ctime   #=> Wed Apr 09 08:53:14 CDT 2003
flock(locking_constant) → 0 or false
Locks or unlocks a file according to locking_constant (a logical or of the values in the table below). Returns false if File::LOCK_NB is specified and the operation would otherwise have blocked. Not available on all platforms.

Locking constants (in class File):

LOCK_EX   | Exclusive lock. Only one process may hold an
          | exclusive lock for a given file at a time.
----------+------------------------------------------------
LOCK_NB   | Don't block when locking. May be combined
          | with other lock options using logical or.
----------+------------------------------------------------
LOCK_SH   | Shared lock. Multiple processes may each hold a
          | shared lock for a given file at the same time.
----------+------------------------------------------------
LOCK_UN   | Unlock.
Example:

# update a counter using write lock
# don't use "w" because it truncates the file before lock.
File.open("counter", File::RDWR|File::CREAT, 0644) {|f|
  f.flock(File::LOCK_EX)
  value = f.read.to_i + 1
  f.rewind
  f.write("#{value}\n")
  f.flush
  f.truncate(f.pos)
}

# read the counter using read lock
File.open("counter", "r") {|f|
  f.flock(File::LOCK_SH)
  p f.read
}
lstat → stat
Same as IO#stat, but does not follow the last symbolic link. Instead, reports on the link itself.

File.symlink("testfile", "link2test")   #=> 0
File.stat("testfile").size              #=> 66
f = File.new("link2test")
f.lstat.size                            #=> 8
f.stat.size                             #=> 66
mtime → time
Returns the modification time for file.

File.new("testfile").mtime   #=> Wed Apr 09 08:53:14 CDT 2003
path → filename
to_path → filename
Returns the pathname used to create file as a string. Does not normalize the name.

The pathname may not point to the file corresponding to file. For instance, the pathname becomes void when the file has been moved or deleted.

This method raises IOError for a file created using File::Constants::TMPFILE because they don't have a pathname.

File.new("testfile").path               #=> "testfile"
File.new("/tmp/../tmp/xxx", "w").path   #=> "/tmp/../tmp/xxx"
size → integer
Returns the size of file in bytes.

File.new("testfile").size   #=> 66
to_path → filename
Returns the pathname used to create file as a string. Does not normalize the name.

The pathname may not point to the file corresponding to file. For instance, the pathname becomes void when the file has been moved or deleted.

This method raises IOError for a file created using File::Constants::TMPFILE because they don't have a pathname.

File.new("testfile").path               #=> "testfile"
File.new("/tmp/../tmp/xxx", "w").path   #=> "/tmp/../tmp/xxx"
truncate(integer) → 0
Truncates file to at most integer bytes. The file must be opened for writing. Not available on all platforms.

f = File.new("out", "w")
f.syswrite("1234567890")   #=> 10
f.truncate(5)              #=> 0
f.close()                  #=> nil
File.size("out")           #=> 5
tools/dev/v8gen.py x64.release.sample
You can inspect and manually edit the build configuration by running:

gn args out.gn/x64.release.sample
Build the static library on a Linux 64 system:

ninja -C out.gn/x64.release.sample v8_monolith
Compile hello-world.cc, linking to the static library created in the build process. For example, on 64bit Linux using the GNU compiler:

g++ -I. -Iinclude samples/hello-world.cc -o hello_world -fno-rtti -lv8_monolith -lv8_libbase -lv8_libplatform -ldl -Lout.gn/x64.release.sample/obj/ -pthread -std=c++17 -DV8_COMPRESS_POINTERS -DV8_ENABLE_SANDBOX
For more complex code, V8 fails without an ICU data file. Copy this file to where your binary is stored:

cp out.gn/x64.release.sample/icudtl.dat .
Run the hello_world executable file at the command line. e.g. On Linux, in the V8 directory, run:

./hello_world

git config --global --unset gpg.format
Use the gpg --list-secret-keys --keyid-format=long command to list the long form of the GPG keys for which you have both a public and private key. A private key is required for signing commits or tags.
Shell
gpg --list-secret-keys --keyid-format=long
Note: Some GPG installations on Linux may require you to use gpg2 --list-keys --keyid-format LONG to view a list of your existing keys instead. In this case you will also need to configure Git to use gpg2 by running git config --global gpg.program gpg2.
From the list of GPG keys, copy the long form of the GPG key ID you'd like to use. In this example, the GPG key ID is 3AA5C34371567BD2:
Shell

$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot <hubot@example.com>
ssb   4096R/4BB6D45482678BE3 2016-03-10
To set your primary GPG signing key in Git, paste the text below, substituting in the GPG primary key ID you'd like to use. In this example, the GPG key ID is 3AA5C34371567BD2:
git config --global user.signingkey 3AA5C34371567BD2
Alternatively, when setting a subkey include the ! suffix. In this example, the GPG subkey ID is 4BB6D45482678BE3:
git config --global user.signingkey 4BB6D45482678BE3!
Optionally, to configure Git to sign all commits by default, enter the following command:
git config --global commit.gpgsign true
For more information, see "Signing commits."
If you aren't using the GPG suite, run the following command in the zsh shell to add the GPG key to your .zshrc file, if it exists, or your .zprofile file:
$ if [ -r ~/.zshrc ]; then echo -e '\nexport GPG_TTY=$(tty)' >> ~/.zshrc; \
  else echo -e '\nexport GPG_TTY=$(tty)' >> ~/.zprofile; fi
Alternatively, if you use the bash shell, run this command:
$ if [ -r ~/.bash_profile ]; then echo -e '\nexport GPG_TTY=$(tty)' >> ~/.bash_profile; \
  else echo -e '\nexport GPG_TTY=$(tty)' >> ~/.profile; fi
Optionally, to prompt you to enter a PIN or passphrase when required, install pinentry-mac. For example, using Homebrew:
brew install pinentry-mac
echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf
killall gpg-agent
curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import
curl -L \
  -X PUT \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import \
  -d '{"vcs":"subversion","vcs_url":"http://svn.mycompany.com/svn/myproject","vcs_username":"octocat","vcs_password":"secret"}'
curl -L \
  -X PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import \
  -d '{"vcs_username":"octocat","vcs_password":"secret"}'
curl -L \
  -X PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import \
  -d '{"vcs":"tfvc","tfvc_project":"project1","human_name":"project1 (tfs)"}'  

curl -L \
  -X PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/import
curl --request GET \
--url "https://api.github.com/user" \
--header "Accept: application/vnd.github+json" \
--header "Authorization: Bearer USER_ACCESS_TOKEN" \
--header "X-GitHub-Api-Version: 2022-11-28"
curl --request GET \
--url "https://api.github.com/user" \
--header "Accept: application/vnd.github+json" \
--header "Authorization: Bearer USER_ACCESS_TOKEN" \
--header "X-GitHub-Api-Version: 2022-11-28"

  

  






+++NSA Black op +++
SHA-2 nist (ecdsa) cert.
AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBI3oSgxrOEJ+tIJ/n6VYtxQIFvynqlOHpfOAJ4x4OfmMYDkbf8dr6RAuUSf+ZC2HMCujta7EjZ9t+6v08Ue+Cgk= 

 "ssh_certificate_authority_id": "sshca_2bMmWjXfs30PrfyvCsxg79Bqea3",
 "principals": ["inconshreveable.com", "10.2.42.9"],
 "valid_after": "2024-01-23T18:09:15Z",
 "valid_until": "2024-04-22T18:09:15Z",
 "certificate": "ecdsa-sha2-nistp256-cert-v01@openssh.com AAAAKGVjZHNhLXNoYTItbmlzdHAyNTYtY2VydC12MDFAb3BlbnNzaC5jb20AAAAggnhUP6YZ1+Wj/NUNS9wN8yyJPgcDTNigqw0RlxX3HqAAAAAIbmlzdHAyNTYAAABBBI3oSgxrOEJ+tIJ/n6VYtxQIFvynqlOHpfOAJ4x4OfmMYDkbf8dr6RAuUSf+ZC2HMCujta7EjZ9t+6v08Ue+CgkAAAAAAAAAAAAAAAIAAAAhc2hjcnRfMmJNbVdvQUZHVlJiTHhqT3hWWEF2dWNMaUF0AAAAJAAAABNpbmNvbnNocmV2ZWFibGUuY29tAAAACTEwLjIuNDIuOQAAAABlsADLAAAAAGYmp8sAAAAAAAAAAAAAAAAAAAAzAAAAC3NzaC1lZDI1NTE5AAAAIPbm5N4qnn+2CMXtrIfRXvUXDmTgkk/fcBHlR9dDAeY3AAAAUwAAAAtzc2gtZWQyNTUxOQAAAEATCa7CcaUJEVcAm2K7PaqeuJDE+pI+8PzMl+aPb9/YRAA72dMMy5izNNVLb7t7Cfqcyi4IGdd2TLFhFyVyayEE shcrt_2bMmWoAFGVRbLxjOxVXAvucLiAt"

Private key
SHA256:TvOWY3mZWlr9uMgny0PtyVdWFzAfKO98UgFlMzgP+ZA=
tar xzf ./actions-runner-linux-x64-2.313.0.tar.gz
GET https://github.com/login/oauth/authorize
This
https://smee.io/F1FDatOZAJIsTI
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq $>"

curl -v \
 'https://subgraph.satsuma-prod.com/< whsec_Ka3G2XkXDVxzhdrFzG8n2OFq >/<Stripe_M嗯InB拉扯呢agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz
$ npm install --global smee-client
Then the smee command will forward webhooks from smee.io to your local development environment.

$ smee -u https://smee.io/F1FDatOZAJIsTI
For usage info:
webhook this page : https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator
$ smee --help
Use the Node.js client

$ npm install --save smee-client
Then:

const SmeeClient = require('smee-client')

const smee = new SmeeClient({
  source: 'https://smee.io/F1FDatOZAJIsTI',
  target: 'http://localhost:3000/events',
  logger: console
})

const events = smee.start()

// Stop forwarding events
events.close()
Using Probot's built-in support

$ npm install --save smee-client
Then set the environment variable:

WEBHOOK_PROXY_URL=https://smee.io/F1FDatOZAJIsTI
POST https://github.com/login/oauth/access_token

access_token=gho_16C7e42F292c6912E7710c838347Ae178B4a&scope=repo%2Cgist&token_type=bearer

Accept: application/json
{
  "access_token":"gho_16C7e42F292c6912E7710c838347Ae178B4a",
  "scope":"repo,gist",
  "token_type":"bearer"
}
Accept: application/xml
<OAuth>
  <token_type>bearer</token_type>
  <scope>repo,gist</scope>
  <access_token>gho_16C7e42F292c6912E7710c838347Ae178B4a</access_token>
</OAuth>
3. Use the access token to access the API

The access token allows you to make requests to the API on a behalf of a user.

Authorization: GitHub Runner token --token  --token A4D7THPUNN2ZH4SELLKJOITF3JIJS
GET https://api.github.com/user
For example, in curl you can set the Authorization header like this:

curl -H "Authorization: GitHub Runner token --token  --token A4D7THPUNN2ZH4SELLKJOITF3JIJS" https://api.github.com/user

POST https://github.com/login/device/code

device_code=3584d83530557fdd1f46af8289938c8ef79f9dc5&expires_in=900&interval=5&user_code=WDJB-MJHT&verification_uri=https%3A%2F%2Fgithub.com%2Flogin%2Fdevice

Accept: application/json
{
  "device_code": "3584d83530557fdd1f46af8289938c8ef79f9dc5",
  "user_code": "WDJB-MJHT",
  "verification_uri": "https://github.com/login/device",
  "expires_in": 900,
  "interval": 5
}
Accept: application/xml
<OAuth>
  <device_code>3584d83530557fdd1f46af8289938c8ef79f9dc5</device_code>
  <user_code>WDJB-MJHT</user_code>
  <verification_uri>https://github.com/login/device</verification_uri>
  <expires_in>900</expires_in>
  <interval>5</interval>
</OAuth>
Step 2: Prompt the user to enter the user code in a browser

Your device will show the user verification code and prompt the user to enter the code at https://github.com/login/device.

Step 3: App polls GitHub to check if the user authorized the device

POST https://github.com/login/oauth/access_token
Your app will make device authorization requests that poll POST https://github.com/login/oauth/access_token, until the device and user codes expire or the user has successfully authorized the app with a valid user code. The app must use the minimum polling interval retrieved in step 1 to avoid rate limit errors. For more information, see "Rate limits for the device flow."

The user must enter a valid code within 15 minutes (or 900 seconds). After 15 minutes, you will need to request a new device authorization code with POST https://github.com/login/device/code.

Once the user has authorized, the app will receive an access token that can be used to make requests to the API on behalf of a user.

The endpoint takes the following input parameters.

Parameter name Type Description
client_id string Required. The client ID you received from GitHub for your OAuth app.
device_code string Required. The device_code you received from the POST https://github.com/login/device/code request.
grant_type string Required. The grant type must be urn:ietf:params:oauth:grant-type:device_code.
By default, the response takes the following form:

access_token=gho_16C7e42F292c6912E7710c838347Ae178B4a&token_type=bearer&scope=repo%2Cgist
You can also receive the response in different formats if you provide the format in the Accept header. For example, Accept: application/json or Accept: application/xml:

Accept: application/json
{
 "access_token": "gho_16C7e42F292c6912E7710c838347Ae178B4a",


contract   graph init \      --product hosted-service     --from-contract &lt;CONTRACT_ADDRESS> \      [--network &lt;ETHEREUM_NETWORK>] \     [--abi &lt;FILE>] \      &lt;subgraph name>
cd <SUBGRAPH_DIRECTORY>
DEPLOY_KEY=dummy_key VERSION_LABEL=v0.0.3 npm run deploy
graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <skf75fXbMunwJ> \
  --ipfs https://ipfs.satsuma.xyz

  DEPLOY_KEY=dummy_key VERSION_LABEL=v0.0.3 npm run deploy
cd <SUBGRAPH_DIRECTORY>
npm install -g @graphprotocol/graph-cli
Create a new subgraph:

Bash

graph init --product hosted-service
Make modifications as necessary to the manifest, schema, and handlers.
See Developing a Subgraph for more details.
Deploying your subgraph

Get your deploy key from your Alchemy Dashboard.
Run the following:

Bash

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <DEPLOY_KEY>
  --ipfs https://ipfs.satsuma.xyz
graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <DEPLOY_KEY> \
  --ipfs https://ipfs.satsuma.xyz

Install the graph-cli:

Bash

npm install -g @graphprotocol/graph-cli
Create a new subgraph:

Bash

graph init --product hosted-service
Make modifications as necessary to the manifest, schema, and handlers.
See Developing a Subgraph for more details.
Deploying your subgraph

Get your deploy key from your Alchemy Dashboard.
Run the following:

Bash

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key <skf75fXbMunwJ>
  --ipfs https://ipfs.satsuma.xyz

  import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": {
    "address": "0x3f5ce5fbfe3e9af3971dd833d26ba9b5c936f0be",
    "tokenBalances": [
      {
        "contractAddress": "0x0000000000085d4780b73119b644ae5ecd22b376",
        "tokenBalance": "0x0000000000000000000000000000000000000000000000000000000000000000"
      },
      ......
      {
        "contractAddress": "0x0abefb7611cb3a01ea3fad85f33c3c934f8e2cf4",
        "tokenBalance": "0x00000000000000000000000000000000000000000000039e431953bcb76c0000"
      },
      {
        "contractAddress": "0x0ad0ad3db75ee726a284cfafa118b091493938ef",
        "tokenBalance": "0x0000000000000000000000000000000000000000008d00cf60e47f03a33fe6e3"
      }
    ],
    "pageKey": "0x0ad0ad3db75ee726a284cfafa118b091493938ef"
  }
}
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'
curl https://eth-mainnet.g.alchemy.com/v2/demo \
-X POST \
-H "Content-Type: application/json" \
-d '{"jsonrpc":"2.0","method":"alchemy_getTokenBalances","params": ["0x3f5ce5fbfe3e9af3971dd833d26ba9b5c936f0be", "erc20"],"id":"42"}'
{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz

import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <skf75fXbMunwJ>

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <skf75fXbMunwJ>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz  



  import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
  curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz

import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz

  curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma

npm install -g @graphprotocol/graph-cli

graph init --product hosted-service

cd <SUBGRAPH_DIRECTORY>

graph deploy <SUBGRAPH_NAME> \
  --version-label <VERSION_NAME> \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
  --ipfs https://ipfs.satsuma.xyz

import { AlchemySigner } from "@alchemy/aa-alchemy";

export const signer = new AlchemySigner({
  client: {
    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
    // here to point to your backend.
    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
    iframeConfig: {
      // you will need to render a container with this id in your DOM
      iframeContainerId: "turnkey-iframe-container",
    },
  },
});
import { AlchemySigner } from "@alchemy/aa-alchemy";
import { useMutation } from "@tanstack/react-query";
import { useEffect, useMemo, useState } from "react";

export const SignupLoginComponent = () => {
  const [email, setEmail] = useState<string>("");

  // It is recommended you wrap this in React Context or other state management
  const signer = useMemo(
    () =>
      new AlchemySigner({
        client: {
          connection: {
            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
          },
          iframeConfig: {
            iframeContainerId: "turnkey-iframe-container",
          },
        },
      }),
    []
  );

  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
  const { mutate: loginOrSignup, isLoading } = useMutation({
    mutationFn: (email: string) =>
      signer.authenticate({ type: "email", email }),
  });

  useEffect(() => {
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("bundle")) {
      // this will complete email auth
      signer
        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
        // redirect the user or do w/e you want once the user is authenticated
        .then(() => (window.location.href = "/"));
    }
  }, [signer]);

  // The below view allows you to collect the email from the user
  return (
    <>
      {!isLoading && (
        <div>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
          <button onClick={() => loginOrSignup(email)}>Submit</button>
        </div>
      )}
      <div id="turnkey-iframe-container" />
    </>
  );
};
import { signer } from "./signer";

// NOTE: this method throws if there is no authenticated user
// so we return null in the case of an error
const user = await signer.getAuthDetails().catch(() => null);

import { signer } from "./signer";

export const account = await createMultiOwnerModularAccount({
  transport: rpcTransport,
  chain,
  signer,
});
import { signer } from "./signer";
import { createWalletClient, http } from "viem";
import { sepolia } from "@alchemy/aa-core";

export const walletClient = createWalletClient({
  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
  chain: sepolia,
  account: signer.toViemAccount(),
});

$ python -m pip install requests
import requests

url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"

headers = {
    "accept": "application/json",
    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
}

response = requests.get(url, headers=headers)

print(response.text)

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
 -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'

curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
 -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"

curl -v \
 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

curl -v \
 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
  --data-raw '{"query":"{entities(first:1){id}}"}'

{
  "data": {
    "indexingStatusForCurrentVersion": {
      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
      "synced": true,
      "health": "healthy",
      "fatalError": null,
      "nonFatalErrors": [],
      "chains": [
        {
          "chainHeadBlock": {
            "number": "17787217"
          },
          "latestBlock": {
            "number": "17787217"
          }
        }
      ]
    }
  }
}

graph deploy example-subgraph-name \
  --version-label v0.0.1-new-version \
  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
  --deploy-key skf75fXbMunwJ \
  --ipfs https://ipfs.satsuma.xyz
  Alchemy Webhook signing key whsec_Ka3G2XkXDVxzhdrFzG8n2OFq

Alchemy api key 2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby

Auth token alchemy webhooks jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp
[PATCH] Update README.md

---
 README.md | 861 +++++++++++++++++++++++++++++++++++++++++++++++++++++-
 1 file changed, 859 insertions(+), 2 deletions(-)

diff --git a/README.md b/README.md
index 317f73a..14583eb 100644
--- a/README.md
+++ b/README.md
@@ -1,7 +1,223 @@
 # FamousToday
-contract   graph init \      --product hosted-service     --from-contract &lt;CONTRACT_ADDRESS> \      [--network &lt;ETHEREUM_NETWORK>] \     [--abi &lt;FILE>] \      &lt;subgraph name>
+Alchemy signing key webhook 
+1. whsec_Ka3G2XkXDVxzhdrFzG8n2OFq
+
+2. whsec_rEV4KKHw57OALQ73encoFHDB ethermeum 
+
+3. whsec_pa1W66wlvZyfLuESqE939OxD polygon matic
+
+"X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
+
+Alchemy Auth token : jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp
+
+Alchemy Webhook identifications
+wh_pae2ekjly3q7fhx9 
+Ethereum Mainnet
+active
+https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator
+
+V2 wh_cktmaceotb7zou0i 
+Polygon Mainnet
+
+Alchemy Webhook signing key whsec_Ka3G2XkXDVxzhdrFzG8n2OFq
+
+Alchemy api key 2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby
+
+Auth token alchemy webhooks jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp
+gem 'jwt', '~> 2.8', '>= 2.8.1'
+
+gem install jwt
+
+gem 'base64', '~> 0.2.0'
+
+gem install base64
+gem 'bundler', '~> 2.5', '>= 2.5.6'
+
+gem install bundler
+
+gem 'rubocop', '~> 1.62', '>= 1.62.1'
+gem install rubocop
+$ gem update --system
+ruby setup.rb --help
+
++++NSA Black op +++
+SHA-2 nist (ecdsa) cert.
+AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBI3oSgxrOEJ+tIJ/n6VYtxQIFvynqlOHpfOAJ4x4OfmMYDkbf8dr6RAuUSf+ZC2HMCujta7EjZ9t+6v08Ue+Cgk= 
+
+ "ssh_certificate_authority_id": "sshca_2bMmWjXfs30PrfyvCsxg79Bqea3",
+ "principals": ["inconshreveable.com", "10.2.42.9"],
+ "valid_after": "2024-01-23T18:09:15Z",
+ "valid_until": "2024-04-22T18:09:15Z",
+ "certificate": "ecdsa-sha2-nistp256-cert-v01@openssh.com AAAAKGVjZHNhLXNoYTItbmlzdHAyNTYtY2VydC12MDFAb3BlbnNzaC5jb20AAAAggnhUP6YZ1+Wj/NUNS9wN8yyJPgcDTNigqw0RlxX3HqAAAAAIbmlzdHAyNTYAAABBBI3oSgxrOEJ+tIJ/n6VYtxQIFvynqlOHpfOAJ4x4OfmMYDkbf8dr6RAuUSf+ZC2HMCujta7EjZ9t+6v08Ue+CgkAAAAAAAAAAAAAAAIAAAAhc2hjcnRfMmJNbVdvQUZHVlJiTHhqT3hWWEF2dWNMaUF0AAAAJAAAABNpbmNvbnNocmV2ZWFibGUuY29tAAAACTEwLjIuNDIuOQAAAABlsADLAAAAAGYmp8sAAAAAAAAAAAAAAAAAAAAzAAAAC3NzaC1lZDI1NTE5AAAAIPbm5N4qnn+2CMXtrIfRXvUXDmTgkk/fcBHlR9dDAeY3AAAAUwAAAAtzc2gtZWQyNTUxOQAAAEATCa7CcaUJEVcAm2K7PaqeuJDE+pI+8PzMl+aPb9/YRAA72dMMy5izNNVLb7t7Cfqcyi4IGdd2TLFhFyVyayEE shcrt_2bMmWoAFGVRbLxjOxVXAvucLiAt"
+
+Private key
+SHA256:TvOWY3mZWlr9uMgny0PtyVdWFzAfKO98UgFlMzgP+ZA=
+tar xzf ./actions-runner-linux-x64-2.313.0.tar.gz
+GET https://github.com/login/oauth/authorize
+This
+https://smee.io/F1FDatOZAJIsTI
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
+ -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
+ -H "Content-Type: application/json" -H "x-api-key: < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq $>"
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/< whsec_Ka3G2XkXDVxzhdrFzG8n2OFq >/<Stripe_M嗯InB拉扯呢agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+{
+  "data": {
+    "indexingStatusForCurrentVersion": {
+      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
+      "synced": true,
+      "health": "healthy",
+      "fatalError": null,
+      "nonFatalErrors": [],
+      "chains": [
+        {
+          "chainHeadBlock": {
+            "number": "17787217"
+          },
+          "latestBlock": {
+            "number": "17787217"
+          }
+        }
+      ]
+    }
+  }
+}
+
+graph deploy example-subgraph-name \
+  --version-label v0.0.1-new-version \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key skf75fXbMunwJ \
+  --ipfs https://ipfs.satsuma
+
+npm install -g @graphprotocol/graph-cli
+
+graph init --product hosted-service
+
 cd <SUBGRAPH_DIRECTORY>
 
+graph deploy <SUBGRAPH_NAME> \
+  --version-label <VERSION_NAME> \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
+  --ipfs https://ipfs.satsuma.xyz
+$ npm install --global smee-client
+Then the smee command will forward webhooks from smee.io to your local development environment.
+
+$ smee -u https://smee.io/F1FDatOZAJIsTI
+For usage info:
+webhook this page : https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator
+$ smee --help
+Use the Node.js client
+
+$ npm install --save smee-client
+Then:
+
+const SmeeClient = require('smee-client')
+
+const smee = new SmeeClient({
+  source: 'https://smee.io/F1FDatOZAJIsTI',
+  target: 'http://localhost:3000/events',
+  logger: console
+})
+
+const events = smee.start()
+
+// Stop forwarding events
+events.close()
+Using Probot's built-in support
+
+$ npm install --save smee-client
+Then set the environment variable:
+
+WEBHOOK_PROXY_URL=https://smee.io/F1FDatOZAJIsTI
+POST https://github.com/login/oauth/access_token
+
+access_token=gho_16C7e42F292c6912E7710c838347Ae178B4a&scope=repo%2Cgist&token_type=bearer
+
+Accept: application/json
+{
+  "access_token":"gho_16C7e42F292c6912E7710c838347Ae178B4a",
+  "scope":"repo,gist",
+  "token_type":"bearer"
+}
+Accept: application/xml
+<OAuth>
+  <token_type>bearer</token_type>
+  <scope>repo,gist</scope>
+  <access_token>gho_16C7e42F292c6912E7710c838347Ae178B4a</access_token>
+</OAuth>
+3. Use the access token to access the API
+
+The access token allows you to make requests to the API on a behalf of a user.
+
+Authorization: GitHub Runner token --token  --token A4D7THPUNN2ZH4SELLKJOITF3JIJS
+GET https://api.github.com/user
+For example, in curl you can set the Authorization header like this:
+
+curl -H "Authorization: GitHub Runner token --token  --token A4D7THPUNN2ZH4SELLKJOITF3JIJS" https://api.github.com/user
+
+POST https://github.com/login/device/code
+
+device_code=3584d83530557fdd1f46af8289938c8ef79f9dc5&expires_in=900&interval=5&user_code=WDJB-MJHT&verification_uri=https%3A%2F%2Fgithub.com%2Flogin%2Fdevice
+
+Accept: application/json
+{
+  "device_code": "3584d83530557fdd1f46af8289938c8ef79f9dc5",
+  "user_code": "WDJB-MJHT",
+  "verification_uri": "https://github.com/login/device",
+  "expires_in": 900,
+  "interval": 5
+}
+Accept: application/xml
+<OAuth>
+  <device_code>3584d83530557fdd1f46af8289938c8ef79f9dc5</device_code>
+  <user_code>WDJB-MJHT</user_code>
+  <verification_uri>https://github.com/login/device</verification_uri>
+  <expires_in>900</expires_in>
+  <interval>5</interval>
+</OAuth>
+Step 2: Prompt the user to enter the user code in a browser
+
+Your device will show the user verification code and prompt the user to enter the code at https://github.com/login/device.
+
+Step 3: App polls GitHub to check if the user authorized the device
+
+POST https://github.com/login/oauth/access_token
+Your app will make device authorization requests that poll POST https://github.com/login/oauth/access_token, until the device and user codes expire or the user has successfully authorized the app with a valid user code. The app must use the minimum polling interval retrieved in step 1 to avoid rate limit errors. For more information, see "Rate limits for the device flow."
+
+The user must enter a valid code within 15 minutes (or 900 seconds). After 15 minutes, you will need to request a new device authorization code with POST https://github.com/login/device/code.
+
+Once the user has authorized, the app will receive an access token that can be used to make requests to the API on behalf of a user.
+
+The endpoint takes the following input parameters.
+
+Parameter name Type Description
+client_id string Required. The client ID you received from GitHub for your OAuth app.
+device_code string Required. The device_code you received from the POST https://github.com/login/device/code request.
+grant_type string Required. The grant type must be urn:ietf:params:oauth:grant-type:device_code.
+By default, the response takes the following form:
+
+access_token=gho_16C7e42F292c6912E7710c838347Ae178B4a&token_type=bearer&scope=repo%2Cgist
+You can also receive the response in different formats if you provide the format in the Accept header. For example, Accept: application/json or Accept: application/xml:
+
+Accept: application/json
+{
+ "access_token": "gho_16C7e42F292c6912E7710c838347Ae178B4a",
+
+
+contract   graph init \      --product hosted-service     --from-contract &lt;CONTRACT_ADDRESS> \      [--network &lt;ETHEREUM_NETWORK>] \     [--abi &lt;FILE>] \      &lt;subgraph name>
+cd <SUBGRAPH_DIRECTORY>
+DEPLOY_KEY=dummy_key VERSION_LABEL=v0.0.3 npm run deploy
 graph deploy <SUBGRAPH_NAME> \
   --version-label <VERSION_NAME> \
   --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
@@ -9,7 +225,34 @@ graph deploy <SUBGRAPH_NAME> \
   --ipfs https://ipfs.satsuma.xyz
 
   DEPLOY_KEY=dummy_key VERSION_LABEL=v0.0.3 npm run deploy
+cd <SUBGRAPH_DIRECTORY>
+npm install -g @graphprotocol/graph-cli
+Create a new subgraph:
+
+Bash
+
+graph init --product hosted-service
+Make modifications as necessary to the manifest, schema, and handlers.
+See Developing a Subgraph for more details.
+Deploying your subgraph
 
+Get your deploy key from your Alchemy Dashboard.
+Run the following:
+
+Bash
+
+cd <SUBGRAPH_DIRECTORY>
+
+graph deploy <SUBGRAPH_NAME> \
+  --version-label <VERSION_NAME> \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key <DEPLOY_KEY>
+  --ipfs https://ipfs.satsuma.xyz
+graph deploy <SUBGRAPH_NAME> \
+  --version-label <VERSION_NAME> \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key <DEPLOY_KEY> \
+  --ipfs https://ipfs.satsuma.xyz
 
 Install the graph-cli:
 
@@ -417,5 +660,619 @@ graph deploy example-subgraph-name \
   --version-label v0.0.1-new-version \
   --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
   --deploy-key skf75fXbMunwJ \
-  --ipfs https://ipfs.satsuma.xyz
+  --ipfs https://ipfs.satsuma.xyz  
+
+
+
+  import { AlchemySigner } from "@alchemy/aa-alchemy";
+
+export const signer = new AlchemySigner({
+  client: {
+    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
+    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
+    // here to point to your backend.
+    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
+    iframeConfig: {
+      // you will need to render a container with this id in your DOM
+      iframeContainerId: "turnkey-iframe-container",
+    },
+  },
+});
+import { AlchemySigner } from "@alchemy/aa-alchemy";
+import { useMutation } from "@tanstack/react-query";
+import { useEffect, useMemo, useState } from "react";
+
+export const SignupLoginComponent = () => {
+  const [email, setEmail] = useState<string>("");
+
+  // It is recommended you wrap this in React Context or other state management
+  const signer = useMemo(
+    () =>
+      new AlchemySigner({
+        client: {
+          connection: {
+            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
+          },
+          iframeConfig: {
+            iframeContainerId: "turnkey-iframe-container",
+          },
+        },
+      }),
+    []
+  );
+
+  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
+  const { mutate: loginOrSignup, isLoading } = useMutation({
+    mutationFn: (email: string) =>
+      signer.authenticate({ type: "email", email }),
+  });
+
+  useEffect(() => {
+    const urlParams = new URLSearchParams(window.location.search);
+    if (urlParams.has("bundle")) {
+      // this will complete email auth
+      signer
+        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
+        // redirect the user or do w/e you want once the user is authenticated
+        .then(() => (window.location.href = "/"));
+    }
+  }, [signer]);
+
+  // The below view allows you to collect the email from the user
+  return (
+    <>
+      {!isLoading && (
+        <div>
+          <input
+            type="email"
+            value={email}
+            onChange={(e) => setEmail(e.target.value)}
+          />
+          <button onClick={() => loginOrSignup(email)}>Submit</button>
+        </div>
+      )}
+      <div id="turnkey-iframe-container" />
+    </>
+  );
+};
+import { signer } from "./signer";
+
+// NOTE: this method throws if there is no authenticated user
+// so we return null in the case of an error
+const user = await signer.getAuthDetails().catch(() => null);
+
+import { signer } from "./signer";
+
+export const account = await createMultiOwnerModularAccount({
+  transport: rpcTransport,
+  chain,
+  signer,
+});
+import { signer } from "./signer";
+import { createWalletClient, http } from "viem";
+import { sepolia } from "@alchemy/aa-core";
+
+export const walletClient = createWalletClient({
+  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
+  chain: sepolia,
+  account: signer.toViemAccount(),
+});
+
+$ python -m pip install requests
+import requests
+
+url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"
+
+headers = {
+    "accept": "application/json",
+    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
+}
+
+response = requests.get(url, headers=headers)
+
+print(response.text)
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
+ -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
+ -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+{
+  "data": {
+    "indexingStatusForCurrentVersion": {
+      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
+      "synced": true,
+      "health": "healthy",
+      "fatalError": null,
+      "nonFatalErrors": [],
+      "chains": [
+        {
+          "chainHeadBlock": {
+            "number": "17787217"
+          },
+          "latestBlock": {
+            "number": "17787217"
+          }
+        }
+      ]
+    }
+  }
+}
+
+graph deploy example-subgraph-name \
+  --version-label v0.0.1-new-version \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key skf75fXbMunwJ \
+  --ipfs https://ipfs.satsuma.xyz
+  curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
+ -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
+ -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+{
+  "data": {
+    "indexingStatusForCurrentVersion": {
+      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
+      "synced": true,
+      "health": "healthy",
+      "fatalError": null,
+      "nonFatalErrors": [],
+      "chains": [
+        {
+          "chainHeadBlock": {
+            "number": "17787217"
+          },
+          "latestBlock": {
+            "number": "17787217"
+          }
+        }
+      ]
+    }
+  }
+}
+
+graph deploy example-subgraph-name \
+  --version-label v0.0.1-new-version \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key skf75fXbMunwJ \
+  --ipfs https://ipfs.satsuma
+
+npm install -g @graphprotocol/graph-cli
+
+graph init --product hosted-service
+
+cd <SUBGRAPH_DIRECTORY>
+
+graph deploy <SUBGRAPH_NAME> \
+  --version-label <VERSION_NAME> \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
+  --ipfs https://ipfs.satsuma.xyz
+
+import { AlchemySigner } from "@alchemy/aa-alchemy";
+
+export const signer = new AlchemySigner({
+  client: {
+    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
+    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
+    // here to point to your backend.
+    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
+    iframeConfig: {
+      // you will need to render a container with this id in your DOM
+      iframeContainerId: "turnkey-iframe-container",
+    },
+  },
+});
+import { AlchemySigner } from "@alchemy/aa-alchemy";
+import { useMutation } from "@tanstack/react-query";
+import { useEffect, useMemo, useState } from "react";
+
+export const SignupLoginComponent = () => {
+  const [email, setEmail] = useState<string>("");
+
+  // It is recommended you wrap this in React Context or other state management
+  const signer = useMemo(
+    () =>
+      new AlchemySigner({
+        client: {
+          connection: {
+            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
+          },
+          iframeConfig: {
+            iframeContainerId: "turnkey-iframe-container",
+          },
+        },
+      }),
+    []
+  );
+
+  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
+  const { mutate: loginOrSignup, isLoading } = useMutation({
+    mutationFn: (email: string) =>
+      signer.authenticate({ type: "email", email }),
+  });
+
+  useEffect(() => {
+    const urlParams = new URLSearchParams(window.location.search);
+    if (urlParams.has("bundle")) {
+      // this will complete email auth
+      signer
+        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
+        // redirect the user or do w/e you want once the user is authenticated
+        .then(() => (window.location.href = "/"));
+    }
+  }, [signer]);
+
+  // The below view allows you to collect the email from the user
+  return (
+    <>
+      {!isLoading && (
+        <div>
+          <input
+            type="email"
+            value={email}
+            onChange={(e) => setEmail(e.target.value)}
+          />
+          <button onClick={() => loginOrSignup(email)}>Submit</button>
+        </div>
+      )}
+      <div id="turnkey-iframe-container" />
+    </>
+  );
+};
+import { signer } from "./signer";
+
+// NOTE: this method throws if there is no authenticated user
+// so we return null in the case of an error
+const user = await signer.getAuthDetails().catch(() => null);
+
+import { signer } from "./signer";
+
+export const account = await createMultiOwnerModularAccount({
+  transport: rpcTransport,
+  chain,
+  signer,
+});
+import { signer } from "./signer";
+import { createWalletClient, http } from "viem";
+import { sepolia } from "@alchemy/aa-core";
+
+export const walletClient = createWalletClient({
+  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
+  chain: sepolia,
+  account: signer.toViemAccount(),
+});
+
+$ python -m pip install requests
+import requests
+
+url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"
+
+headers = {
+    "accept": "application/json",
+    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
+}
+
+response = requests.get(url, headers=headers)
+
+print(response.text)
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
+ -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
+ -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+{
+  "data": {
+    "indexingStatusForCurrentVersion": {
+      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
+      "synced": true,
+      "health": "healthy",
+      "fatalError": null,
+      "nonFatalErrors": [],
+      "chains": [
+        {
+          "chainHeadBlock": {
+            "number": "17787217"
+          },
+          "latestBlock": {
+            "number": "17787217"
+          }
+        }
+      ]
+    }
+  }
+}
+
+graph deploy example-subgraph-name \
+  --version-label v0.0.1-new-version \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key skf75fXbMunwJ \
+  --ipfs https://ipfs.satsuma.xyz
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
+ -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
+ -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+{
+  "data": {
+    "indexingStatusForCurrentVersion": {
+      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
+      "synced": true,
+      "health": "healthy",
+      "fatalError": null,
+      "nonFatalErrors": [],
+      "chains": [
+        {
+          "chainHeadBlock": {
+            "number": "17787217"
+          },
+          "latestBlock": {
+            "number": "17787217"
+          }
+        }
+      ]
+    }
+  }
+}
+
+graph deploy example-subgraph-name \
+  --version-label v0.0.1-new-version \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key skf75fXbMunwJ \
+  --ipfs https://ipfs.satsuma.xyz
+
+  curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
+ -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
+ -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>"
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<Stripe_Men In Black agency>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq Y>' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+{
+  "data": {
+    "indexingStatusForCurrentVersion": {
+      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
+      "synced": true,
+      "health": "healthy",
+      "fatalError": null,
+      "nonFatalErrors": [],
+      "chains": [
+        {
+          "chainHeadBlock": {
+            "number": "17787217"
+          },
+          "latestBlock": {
+            "number": "17787217"
+          }
+        }
+      ]
+    }
+  }
+}
+
+graph deploy example-subgraph-name \
+  --version-label v0.0.1-new-version \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key skf75fXbMunwJ \
+  --ipfs https://ipfs.satsuma
+
+npm install -g @graphprotocol/graph-cli
+
+graph init --product hosted-service
+
+cd <SUBGRAPH_DIRECTORY>
+
+graph deploy <SUBGRAPH_NAME> \
+  --version-label <VERSION_NAME> \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key < whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>
+  --ipfs https://ipfs.satsuma.xyz
+
+import { AlchemySigner } from "@alchemy/aa-alchemy";
+
+export const signer = new AlchemySigner({
+  client: {
+    // This is created in your dashboard under `https://dashboard.alchemy.com/settings/access-keys`
+    // NOTE: it is not recommended to expose your API key on the client, instead proxy requests to your backend and set the `rpcUrl`
+    // here to point to your backend.
+    connection: { apiKey: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>" },
+    iframeConfig: {
+      // you will need to render a container with this id in your DOM
+      iframeContainerId: "turnkey-iframe-container",
+    },
+  },
+});
+import { AlchemySigner } from "@alchemy/aa-alchemy";
+import { useMutation } from "@tanstack/react-query";
+import { useEffect, useMemo, useState } from "react";
+
+export const SignupLoginComponent = () => {
+  const [email, setEmail] = useState<string>("");
+
+  // It is recommended you wrap this in React Context or other state management
+  const signer = useMemo(
+    () =>
+      new AlchemySigner({
+        client: {
+          connection: {
+            jwt: "alcht_<2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby>",
+          },
+          iframeConfig: {
+            iframeContainerId: "turnkey-iframe-container",
+          },
+        },
+      }),
+    []
+  );
+
+  // we are using react-query to handle loading states more easily, but feel free to use w/e state management library you prefer
+  const { mutate: loginOrSignup, isLoading } = useMutation({
+    mutationFn: (email: string) =>
+      signer.authenticate({ type: "email", email }),
+  });
+
+  useEffect(() => {
+    const urlParams = new URLSearchParams(window.location.search);
+    if (urlParams.has("bundle")) {
+      // this will complete email auth
+      signer
+        .authenticate({ type: "email", bundle: urlParams.get("bundle")! })
+        // redirect the user or do w/e you want once the user is authenticated
+        .then(() => (window.location.href = "/"));
+    }
+  }, [signer]);
+
+  // The below view allows you to collect the email from the user
+  return (
+    <>
+      {!isLoading && (
+        <div>
+          <input
+            type="email"
+            value={email}
+            onChange={(e) => setEmail(e.target.value)}
+          />
+          <button onClick={() => loginOrSignup(email)}>Submit</button>
+        </div>
+      )}
+      <div id="turnkey-iframe-container" />
+    </>
+  );
+};
+import { signer } from "./signer";
+
+// NOTE: this method throws if there is no authenticated user
+// so we return null in the case of an error
+const user = await signer.getAuthDetails().catch(() => null);
+
+import { signer } from "./signer";
+
+export const account = await createMultiOwnerModularAccount({
+  transport: rpcTransport,
+  chain,
+  signer,
+});
+import { signer } from "./signer";
+import { createWalletClient, http } from "viem";
+import { sepolia } from "@alchemy/aa-core";
+
+export const walletClient = createWalletClient({
+  transport: http("[alchemy_rpc_url](https://scpf-foundation-roblox.fandom.com/wiki/The_Administrator)"),
+  chain: sepolia,
+  account: signer.toViemAccount(),
+});
+
+$ python -m pip install requests
+import requests
+
+url = "https://dashboard.alchemy.com/api/webhook-addresses?webhook_id=https%3A%2F%2Fscpf-foundation-roblox.fandom.com%2Fwiki%2FThe_Administrator&limit=1000&after=19"
+
+headers = {
+    "accept": "application/json",
+    "X-Alchemy-Token": "2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby"
+}
+
+response = requests.get(url, headers=headers)
+
+print(response.text)
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/promote-live \
+ -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>'
+
+curl -X POST https://subgraphs.alchemy.com/api/subgraphs/<TEAM ID>/<SUBGRAPH_NAME>/<VERSION_NAME>/auto-promote-live \
+ -H "Content-Type: application/json" -H "x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq"
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<QUERY_KEY>/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+curl -v \
+ 'https://subgraph.satsuma-prod.com/<ORGANIZATION>/<SUBGRAPH_NAME>/version/<VERSION_NAME>/api' \
+  -H 'x-api-key: <whsec_Ka3G2XkXDVxzhdrFzG8n2OFq>' \
+  --data-raw '{"query":"{entities(first:1){id}}"}'
+
+{
+  "data": {
+    "indexingStatusForCurrentVersion": {
+      "subgraph": "QmXqNgptc2b5WzwmFfCu8PxsLgetBe5M8eBKvSyu5jqkei",
+      "synced": true,
+      "health": "healthy",
+      "fatalError": null,
+      "nonFatalErrors": [],
+      "chains": [
+        {
+          "chainHeadBlock": {
+            "number": "17787217"
+          },
+          "latestBlock": {
+            "number": "17787217"
+          }
+        }
+      ]
+    }
+  }
+}
+
+graph deploy example-subgraph-name \
+  --version-label v0.0.1-new-version \
+  --node https://subgraphs.alchemy.com/api/subgraphs/deploy \
+  --deploy-key skf75fXbMunwJ \
+  --ipfs https://ipfs.satsuma.xyz
+  Alchemy Webhook signing key whsec_Ka3G2XkXDVxzhdrFzG8n2OFq
+
+Alchemy api key 2_30hERlJhrpl9Tgt1a5sX9D7NA_9cby
+
+Auth token alchemy webhooks jE92Hk8uCBZnJEh1PP0PoUVDwnuYFdVp


  
