# A Complete Set up for localstack

# Install Localstack

> https://github.com/localstack/localstack

### Step 1 - Create a docker compose file
Create a docker-compose.yaml file and place in it the following configuration:

```yaml
version: '2.1'
services:

  localstack:
    image: localstack/localstack
    ports:
      - "4567-4584:4567-4584"
      - "${PORT_WEB_UI-8080}:${PORT_WEB_UI-8080}"
    environment:
      - SERVICES=${SERVICES- }
      - DEBUG=${DEBUG- }
      - DATA_DIR=${DATA_DIR- }
      - PORT_WEB_UI=${PORT_WEB_UI- }
      - LAMBDA_EXECUTOR=${LAMBDA_EXECUTOR- }
      - KINESIS_ERROR_PROBABILITY=${KINESIS_ERROR_PROBABILITY- }
      - DOCKER_HOST=unix:///var/run/docker.sock
    volumes:
      - "./tmp/localstack:/tmp/localstack"
```

### Step 2 - Download and run localstack
From the commandline cd into the the same directory as the docker-compose.yaml file and run the following command:

```bash
docker-compose up -d
```

### Step 3 - Create Credentials for Localstack
> https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html

Localstack does not undertake any authentication, however it requires a credentials profile to operate even though it does not use it.

So let's create a credentials profile with the following command:
 
```bash
aws configure
```

It doesn't really matter what values are supplied so feel free to use the following:
> AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE \
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY \
Default region name [None]: us-west-2 \
Default output format [None]: json

### Step 4 - Add my-domain to the /etc/hosts file

Open the /etc/hosts file

```bash
sudo nano /etc/hosts
```

Add the domain 'my-domain' to the the file as an entry against the loopback address 127.0.0.1

```nano
127.0.0.1       my-domain
```

### Step 5 - Create a bucket called 'mytestbucket'

To issue commands to loaclstack we will need to use the --endpoint-url= flag to direct our commands to the right place.

The following AWS command to create a bucket should be adjusted as follows:

```bash
aws s3 mb s3://mytestbucket
```

Change this to:

```bash
aws --endpoint-url=http://my-domain:4572 s3 mb s3://mytestbucket
```

Check that the bucket is there and has been created

```bash
aws --endpoint-url=http://my-domain:4572 s3 ls
```

We should see something like:

```bash
2006-02-03 16:45:09 mytestbucket
```

### Step 6 - Add some files to the bucket

The command to add files to a bucket follows the pattern below:

> aws < END-POINT > s3 cp < PATH-TO-FILE > < BUCKET >

```bash
aws --endpoint-url=http://my-domain:4572 s3 cp my-file.txt s3://mytestbucket
```

Check that the file is there:

```bash
aws --endpoint-url=http://my-domain:4572 s3 ls s3://mytestbucket
```

You should see something like the folowing:

```bash
2019-09-23 10:00:24        333 my-file-1.txt
2019-09-23 09:56:16        310 my-file-2.txt
2019-09-23 14:36:53       3776 my-file-3.json
2019-09-23 09:59:45       5221 my-file-4.txt
2019-09-23 10:01:16       1243 my-file-5.pdf
```

# AND YOU'RE DONE!
