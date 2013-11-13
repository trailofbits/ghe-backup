ghe-backup
===============================

Encrypted Backups to S3 for GitHub Enterprise.

### Features

* Supports making complete backups from Github Enterprise servers
* Supports arbitrary backup locations
* Supports encrypting backups with GPG
* Supports syncing backups to AWS S3

### Getting Started

1. Setup SSH access on your GHE server
2. Create a PGP key to use for backups
3. (Optional) Configure an AWS S3 user and bucket
4. (Optional) Install and Configure aws-cli
5. Configure `ghe-backup` variables

#### Github Enterprise SSH Access

[Enable SSH access](https://enterprise.github.com/help/articles/ssh-access) to the Github Enterprise server through the management console with a public key from your backup server. Restrict access to only your backup server with the `from` option in your SSH public key. OS X users may want to use [Homebrew](http://brew.sh/) to install GnuPG.

#### GnuPG backup key

[Create a new GPG  key](https://help.ubuntu.com/community/GnuPrivacyGuardHowto) with `gpg --gen-key` and set a blank password. Store an offline backup of your key with `gpg -ao keyname-private.key --export-secret-keys key_id`.

#### Configure AWS S3

Create a new S3 bucket via the AWS management console and use an [IAM policy](http://docs.aws.amazon.com/IAM/latest/UserGuide/PoliciesOverview.html) to restrict its access. Optionally, configure lifecycle rules to automate backups to Glacier.

<pre>{
	"Statement": [
		{
			"Sid": "UNIQUE_ID",
			"Action": "s3:*",
			"Effect": "Allow",
			"Resource": "arn:aws:s3:::BUCKET_NAME/*"
		},
		{
			"Sid": "UNIQUE_ID",
			"Action": ["s3:ListAllMyBuckets"],
			"Effect": "Allow",
			"Resource": "arn:aws:s3:::*"
		},
		{
			"Sid": "UNIQUE_ID",
			"Action": "s3:*",
			"Effect": "Allow",
			"Resource": "arn:aws:s3:::BUCKET_NAME"
		}
	]
}</pre>

#### Setup aws-cli

[Install the AWS Command Line Interface](https://github.com/aws/aws-cli#installation) with `pip install aws-cli`. Create a configuration file in `~/.aws/config` and set the `aws_access_key`, `aws_secret_access_key`, and `region` using the appropriate parameters from the previous step.

#### Configure the script

Set the appropriate `SERVER`, the `BACKUPS_DIR` you want to use, name or email of the `PGP_RECIPIENT`, enable or disable `S3_BACKUP` and the `S3_BUCKET` you want to use. Run `ghe-backup` without arguments to begin the backup. Add it to cron to run as often as you would like.

### More info

See the [GitHub Enterprise documentation](https://enterprise.github.com/help/articles/backing-up-your-installation) for more info about backing up and restoring your installation.
