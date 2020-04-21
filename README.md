# summary

Locally submitted cloudbuild, and presumably other gcloud utilities which create tarballs, do not correctly include relative symlinks to directories.

## reported at

https://issuetracker.google.com/issues/154532065

## similar reports

[Symlinks to directories ignored](https://issuetracker.google.com/issues/70571662)
[App engine ignores symlinks to directories](https://stackoverflow.com/questions/47776608/app-engine-ignores-symlinks-to-directories)
[GCP Container Builder unable to evaluate symlinks](https://stackoverflow.com/questions/42832536/gcp-container-builder-unable-to-evaluate-symlinks)

## hypothesis

`gcloud builds submit` tar process does not include the symlinks. Local gnu tar works fine and correctly creates a relative symlink by default.

## gcloud builds submit failure

```
gcloud beta builds submit
Creating temporary tarball archive of 2 file(s) totalling 148 bytes before compression.
Uploading tarball of [.] to [gs://project-redacted-240911_cloudbuild/source/1587444469.6-a1f88746247a4a86949c2c7f8ef231c8.tgz]
Created [https://cloudbuild.googleapis.com/v1/projects/project-redacted-240911/builds/2a478ec8-55b3-4229-8d43-41e8904b263c].
Logs are available at [https://console.cloud.google.com/cloud-build/builds/2a478ec8-55b3-4229-8d43-41e8904b263c?project=239151533311].
------------------------------------ REMOTE BUILD OUTPUT -------------------------------------
starting build "2a478ec8-55b3-4229-8d43-41e8904b263c"

FETCHSOURCE
Fetching storage object: gs://project-redacted-240911_cloudbuild/source/1587444469.6-a1f88746247a4a86949c2c7f8ef231c8.tgz#1587444470442989
Copying gs://project-redacted-240911_cloudbuild/source/1587444469.6-a1f88746247a4a86949c2c7f8ef231c8.tgz#1587444470442989...
/ [1 files][  266.0 B/  266.0 B]
Operation completed over 1 objects/266.0 B.
BUILD
Starting Step #0
Step #0: Pulling image: alpine
Step #0: Using default tag: latest
Step #0: latest: Pulling from library/alpine
Step #0: Digest: sha256:cb8a924afdf0229ef7515d9e5b3024e23b3eb03ddbba287f4a19c6ac90b8d221
Step #0: Status: Downloaded newer image for alpine:latest
Step #0: docker.io/library/alpine:latest
Step #0: bar
Finished Step #0
Starting Step #1
Step #1: Already have image: alpine
Step #1: cat: can't open 'child/stuff/foo': No such file or directory
Finished Step #1
ERROR
ERROR: build step 1 "alpine" failed: step exited with non-zero status: 1
----------------------------------------------------------------------------------------------

ERROR: (gcloud.beta.builds.submit) build 2a478ec8-55b3-4229-8d43-41e8904b263c completed with status "FAILURE"
```

## github app trigger success

```
starting build "c21fe951-c86b-464b-b409-0e928c596005"

FETCHSOURCE
Fetching storage object: gs://876943951848.cloudbuild-source.googleusercontent.com/bb4b3dc2a7c523f6144a90d2c71b12101cb97573-f78d1518-c109-412b-861b-578c9fc036b8.tar.gz#1587445382436887
Copying gs://876943951848.cloudbuild-source.googleusercontent.com/bb4b3dc2a7c523f6144a90d2c71b12101cb97573-f78d1518-c109-412b-861b-578c9fc036b8.tar.gz#1587445382436887...
/ [0 files][    0.0 B/  1.4 KiB]
/ [1 files][  1.4 KiB/  1.4 KiB]
Operation completed over 1 objects/1.4 KiB.
BUILD
Starting Step #0
Step #0: Pulling image: alpine
Step #0: Using default tag: latest
Step #0: latest: Pulling from library/alpine
Step #0: Digest: sha256:cb8a924afdf0229ef7515d9e5b3024e23b3eb03ddbba287f4a19c6ac90b8d221
Step #0: Status: Downloaded newer image for alpine:latest
Step #0: docker.io/library/alpine:latest
Step #0: bar
Finished Step #0
Starting Step #1
Step #1: Already have image: alpine
Step #1: bar
Finished Step #1
PUSH
DONE
```

## gcloud version
Google Cloud SDK 289.0.0
alpha 2020.04.10
beta 2020.04.10
bq 2.0.56
cloud-build-local
core 2020.04.10
gsutil 4.49
kubectl 2020.04.10

## extraction of gcloud produced tarball has no symlink

```
$ tar -xvzf source_1587445587.49-ce00ae3341c0422891c58bf40bd0c0de.tar
stuff/
child/
child/stuff/
stuff/foo
cloudbuild.yaml
README.md

$ tree
.
├── child
│   └── stuff
├── cloudbuild.yaml
├── README.md
├── source_1587445587.49-ce00ae3341c0422891c58bf40bd0c0de.tar
└── stuff
    └── foo

$ cat child/stuff/foo
cat: child/stuff/foo: No such file or directory

$ ll child/stuff
total 8.0K
drwxr-xr-x 2 afirth afirth 4.0K Jan  1  1970 .
drwxr-xr-x 3 afirth afirth 4.0K Jan  1  1970 ..
```

## local gnu tarball works fine

```
$ tar --version
tar (GNU tar) 1.29

$ tar --exclude-vcs -czvf /tmp/symlinks.tgz symlinks-cloudbuild

symlinks-cloudbuild/
symlinks-cloudbuild/README.md
symlinks-cloudbuild/stuff/
symlinks-cloudbuild/stuff/foo
symlinks-cloudbuild/child/
symlinks-cloudbuild/child/stuff
symlinks-cloudbuild/.README.md.swp
symlinks-cloudbuild/cloudbuild.yaml

$ cd /tmp

$ tar -xvzf symlinks.tgz
symlinks-cloudbuild/
symlinks-cloudbuild/README.md
symlinks-cloudbuild/stuff/
symlinks-cloudbuild/stuff/foo
symlinks-cloudbuild/child/
symlinks-cloudbuild/child/stuff
symlinks-cloudbuild/.README.md.swp
symlinks-cloudbuild/cloudbuild.yaml


$ tree symlinks-cloudbuild
symlinks-cloudbuild
├── child
│   └── stuff -> ../stuff
├── cloudbuild.yaml
├── README.md
└── stuff
    └── foo

3 directories, 3 files
$ cat symlinks-cloudbuild/child/stuff/foo
bar
```
