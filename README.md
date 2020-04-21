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

## gcloud version
Google Cloud SDK 289.0.0
alpha 2020.04.10
beta 2020.04.10
bq 2.0.56
cloud-build-local
core 2020.04.10
gsutil 4.49
kubectl 2020.04.10

