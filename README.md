# WAL-G

WAL-G 

**Table of Contents**  
- [Installation](#installation)  
- [Configuration](#configuration)  
- [Usage](#usage)  
- [Development](#development)  
	- [Installing](#installing)  
	- [Testing](#testing)

Installation
----------
A precompiled binary for Linux AMD 64 of the latest version of WAL-G can be obtained like so:

```
curl -s https://api.github.com/repos/wal-g/wal-g/releases/latest \
  | grep browser_download_url \
  | grep amd64 \
  | cut -d '"' -f 4 \
  | wget -qi -
tar -zxvf wal-g.linux-amd64.tar.gz
```
For other incompatible systems, please consult the Development section for more information.

Configuration
-------------
**Required**

To connect to Amazon S3, WAL-G requires that these variables be set:

* `WALE_S3_PREFIX` (eg. `s3://bucket/path/to/folder`)
* `AWS_REGION`(eg. `us-west-2`)
* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`

Also note that WAL-G uses this environment variable to connect to Postgres:

* `PGHOST`

**Optional**

Required if using AWS STS:

* `AWS_SECURITY_TOKEN`

Concurrency values can be configured using:

* `WALG_MAX_CONCURRENCY`

To configure how many goroutines to use during extraction, use `WALG_MAX_CONCURRENCY`. By default, WAL-G uses the minimum of the number of files to extract and 10.

* `WALG_UPLOAD_CONCURRENCY`

To configure how many concurrency streams to use during backup uploading, use `WALG_UPLOAD_CONCURRENCY`. By default, WAL-G uses 10 streams.



Usage
-----

WAL-G currently supports these commands:


* ``backup-fetch``

When fetching base backups, the user should pass in the name of the backup and a path to a directory to extract to. If this directory does not exist, WAL-G will create it and any dependent subdirectories. 

```
wal-g backup-fetch ~/extract/to/here example-backup
```

WAL-G can also fetch the latest backup using:

```
wal-g backup-fetch ~/extract/to/here LATEST
```

* ``backup-push``

When uploading backups to S3, the user should pass in the path containing the backup started by Postgres as in:

```
wal-g backup-push /backup/directory/path
```


* ``wal-fetch``

When fetching WAL archives from S3, the user should pass in the archive name and the name of the file to download to. This file should not exist as WAL-G will create it for you.

```
wal-g wal-fetch example-archive new-file-name
```


* ``wal-push``

When uploading WAL archives to S3, the user should pass in the absolute path to where the archive is located.

```
wal-g wal-push /path/to/archive
```

Development
-----------
### Installing

To compile and build the binary:

```
go get github.com/wal-g/wal-g
make all
```
Users can also install WAL-G by using `make install`. Specifying the GOBIN environment variable before installing allows the user to specify the installation location. On default, `make install` puts the compiled binary in `go/bin`.

```
export GOBIN=/usr/local/bin
make install
```

### Testing

WAL-G relies heavily on unit tests. These tests do not require S3 configuration as the upload/download parts are tested using mocked objects. For more information on testing, please consult [test_tools](test_tools).

WAL-G will preform a round-trip compression/decompression test that generates a directory for data (eg. data...), compressed files (eg. compressed), and extracted files (eg. extracted). These directories will only get cleaned up if the files in the original data directory matches the files in the extracted one.

Test coverage can be obtained using:

```
go test -v -coverprofile=coverage.out
go tool cover -html=coverage.out
```


Authors
-------

* [Katie Li](https://github.com/katie31)
* [Daniel Farina](https://github.com/fdr)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

License
-------

This project is licensed under the Apache License, Version 2.0, but the lzo support is licensed under GPL 3.0+. Please refer to the [LICENSE.md](LICENSE.md) file for more details.

Acknowledgements
----------------

