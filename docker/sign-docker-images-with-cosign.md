# Sign docker images with Cosign

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

#### Prepare environment

```
sudo apt update && sudo apt install -y ca-certificates curl gnupg
```

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

```
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```
sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

```
sudo usermod -aG docker $USER
sudo systemctl enable docker.service
```

```
sync && sudo reboot
```

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

#### Dockerfile (sample)

```docker
FROM openjdk:22-bookworm

COPY artifactId-web-0.0.1-SNAPSHOT-dev /artifactId-web-0.0.1-SNAPSHOT-dev

EXPOSE 8080
CMD ["/artifactId-web-0.0.1-SNAPSHOT-dev"]
```

#### Build image docker

```sh
docker build -t repository/my-image:tag .
```

#### View image docker

```sh
docker images
```

#### Run image docker

```sh
docker run -d -p 8080:8080 repository/my-image:tag --name my-image
```

#### View logs imagen docker

```sh
docker logs -f my-image
```

###

### Push image docker to Docker Hub

```sh
docker login
```

```sh
docker push repository/my-image:tag
```

###

### Sign image docker

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Install cosign

[https://github.com/sigstore/cosign/releases](https://github.com/sigstore/cosign/releases)\
v2.0.0 latest

**Generate public/private keys**

```sh
cosign generate-key-pair
```

or

```sh
echo "your_password" | cosign generate-key-pair
```

Output

```sh
Enter password for private key:
Enter password for private key again:

Are you sure would like to continue [y/N]: y
Private key written to cosign.key
Public key written to cosign.pub
```

\
Option 1, **Image signature with private key**

```sh
 cosign sign --key cosign.key -a "author=AzagraMac" repository/my-image:tag
```

```sh
Enter password for private key:
WARNING: Image reference repository/my-image:tag uses a tag, not a digest, to identify the image to sign.
    This can lead you to sign a different image than the intended one. Please use a
    digest (example.com/ubuntu@sha256:abc123...) rather than tag
    (example.com/ubuntu:latest) for the input to cosign. The ability to refer to
    images by tag will be removed in a future release.

WARNING: "repository/my-image" appears to be a private repository, please confirm uploading to the transparency log at "https://rekor.sigstore.dev"
Are you sure you would like to continue? [y/N] y

        The sigstore service, hosted by sigstore a Series of LF Projects, LLC, is provided pursuant to the Hosted Project Tools Terms of Use, available at https://lfprojects.org/policies/hosted-project-tools-terms-of-use/.
        Note that if your submission includes personal data associated with this signed artifact, it will be part of an immutable record.
        This may include the email address associated with the account with which you authenticate your contractual Agreement.
        This information will be used for signing this artifact and will be stored in public transparency logs and cannot be removed later, and is subject to the Immutable Record notice at https://lfprojects.org/policies/hosted-project-tools-immutable-records/.

By typing 'y', you attest that (1) you are not submitting the personal data of any other person; and (2) you understand and agree to the statement and the Agreement terms at the URLs listed above.
Are you sure you would like to continue? [y/N] y
tlog entry created with index: 35833795
Pushing signature to: repository/my-image
```

\
Option 2, **Image signature using web token**

```sh
 cosign sign repository/my-image:tag
```

```sh
Generating ephemeral keys...
Retrieving signed certificate...

        The sigstore service, hosted by sigstore a Series of LF Projects, LLC, is provided pursuant to the Hosted Project Tools Terms of Use, available at https://lfprojects.org/policies/hosted-project-tools-terms-of-use/.
        Note that if your submission includes personal data associated with this signed artifact, it will be part of an immutable record.
        This may include the email address associated with the account with which you authenticate your contractual Agreement.
        This information will be used for signing this artifact and will be stored in public transparency logs and cannot be removed later, and is subject to the Immutable Record notice at https://lfprojects.org/policies/hosted-project-tools-immutable-records/.

By typing 'y', you attest that (1) you are not submitting the personal data of any other person; and (2) you understand and agree to the statement and the Agreement terms at the URLs listed above.
Are you sure you would like to continue? [y/N] y
error opening browser: exec: "xdg-open": executable file not found in $PATH
Go to the following link in a browser:

         https://oauth2.sigstore.dev/auth/auth?access_type=online&client_id=sigstore&code_challenge=aS3452hjb2iu324d65r652y3fc4&code_challenge_method=S256&nonce=2VHrAAZqX4qAerbvoo9volVdJ5B&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&response_type=code&scope=openid+email&state=s87TD7G6gv655d6imBbEXLcsNTm
Enter verification code: 
```

\
It generates a URL, to log in with our credentials

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

and returns a verification token that must be copied to the terminal

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

and it will sign the image, it generates a new tag in the image that we have in the repository.

```sh
Successfully verified SCT...
WARNING: Image reference repository/my-image:tag uses a tag, not a digest, to identify the image to sign.
    This can lead you to sign a different image than the intended one. Please use a
    digest (example.com/ubuntu@sha256:abc123...) rather than tag
    (example.com/ubuntu:latest) for the input to cosign. The ability to refer to
    images by tag will be removed in a future release.

WARNING: "repository/my-image" appears to be a private repository, please confirm uploading to the transparency log at "https://rekor.sigstore.dev"
Are you sure you would like to continue? [y/N] y
tlog entry created with index: 35822795
Pushing signature to: repository/my-image
```

### Verify a Container’s signature

```sh
cosign verify --key cosign.pub repository/my-image
```

```json
Verification for name_repo.azurecr.io/my-image:tag --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - The signatures were verified against the specified public key

[
   {
      "critical":{
         "identity":{
            "docker-reference":"repository/my-image"
         },
         "image":{
            "docker-manifest-digest":"sha256:d4a46b56ae9ff8ed1d09273ab1c9c9518228198d3467ecde"
         },
         "type":"cosign container image signature"
      },
      "optional":{
         "Bundle":{
            "SignedEntryTimestamp":"MEQCIFaOAj1tLMCepfD48OaxXnAsf2GWh3WtfkgpX1uA==",
            "Payload":{
               "body":"eyJhcGlWZXLjEiLCJraW5kIjoiaGFzaGVkcmVrf3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiJiYmEyYWNmODljYjc2MzA5NmJhMjA1ZTE4MzE1Y2NkNTQxODQ1MDMyOWYwOWY4ZjUwY2JmYWFjZjliZDk3OTE3In19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FVUNJUUQwTjNzU1RRalJoWUN2SStOYWNMNThaNVZjTGJkUnRWeFViZUQrenlxc2pnSWdDODhYZVVkQ0x0YTBrTjV6OWVITExkTUVJdy9rUE9nMTBwL1hhcHdWWjZjPSIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCUVZVSk1TVU1nUzBWWkxTMHRMUzBLVFVacmQwVjNXVWhMYjFwSmVtb3dRMEZSV1VsTGIxcEplbW93UkVGUlkwUlJaMEZGYmxkNVRuTkRjVWhsVVdGdFNsWmxjRkJIV2xaV1luVnNVRXRKVHdwSFVVSk5aWE0wVUZwVlUzQXpUVEUzYlVOSWJraHJjVzV6V1VaNFpHdFlkVlJxYjJWdlNUbHBURFJoVFZSUVNFnVUZWJRXRGV1MwdExTMHRDZz09In19fX0=",
               "integratedTime":1694407129,
               "logIndex":35822795,
               "logID":"c0d23d6ad606973f95594f98b9591801d"
            }
         },
         "author":"AzagraMac"
      }
   }
]
```
