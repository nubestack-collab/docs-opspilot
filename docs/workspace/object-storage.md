# Object storage

An S3 bucket connects as a browsable connection, listed alongside your servers and opened
like any other. It covers the object-storage work that sits next to operations rather than
next to application code: pulling a release artifact, reading an archived log, checking
that last night's backup landed.

## Connecting a bucket

Choose **S3** (AWS S3) as the connection type. The form drops the host, port and
username fields — object storage has no shell — and asks for these instead:

| Field | Notes |
|---|---|
| **region** | Defaults to `us-east-1` if you leave it blank |
| **bucket** | The bucket this connection opens |
| **access key ID** | |
| **secret access key** | Encrypted by the operating system, like a password |
| **custom endpoint (optional)** | For an S3-compatible service; see below |

The connection opens as a tab with two panes rather than a terminal: your own computer on
the left (**Local**) and the bucket on the right. Drag files between them to upload or
download. Where object-storage support is unavailable in the installation, the connection
reports that rather than failing obscurely.

During the free trial and without a subscription, an S3 tab counts toward the 10 sessions
you can have open at once; see [Free trial and limits](../licensing/trial-and-limits.md).

### Connect a bucket, step by step

1. Click **+ new connection** and choose **S3** as the type.
2. Give the connection a name, and fill in **region**, **bucket**, **access key ID** and
   **secret access key**.
3. For a service other than AWS, fill in **custom endpoint (optional)**.
4. Leave **Save connection** on and click **connect**.

## S3-compatible endpoints

Filling in **custom endpoint** points the client at that URL instead of AWS and switches
it to path-style addressing, which is what self-hosted gateways expect. That makes MinIO,
Ceph object gateways and other S3-compatible services reachable with the same connection
type — the credentials and the bucket work the same way, only the endpoint differs.

Leave the endpoint blank for AWS itself.

## Browsing a bucket

A bucket is presented through the same explorer as an SFTP filesystem. Key prefixes
appear as directories and objects as files, so you navigate, open, edit, upload,
download, rename, copy and delete in the interface you already know. Listings are
paginated, so a bucket with a large number of objects browses one level at a time rather
than trying to enumerate everything.

Check both of these before acting on a production bucket:

- **A "folder" is a prefix.** Creating one writes a zero-byte object whose key ends in
  `/`, which is the same convention the AWS console uses.
- **A rename is a copy followed by a delete**, and renaming a prefix does that for every
  object beneath it. It is not an atomic operation, and it is not free on a large prefix.

There is no terminal for an S3 connection, so there is no scrollback for the assistant to
analyse, and AI works only in SSH and Local tabs. To show the assistant an object, download
it and attach it in an SSH or Local tab's AI panel with **+ → Upload from computer**. An
attachment goes through the same redaction layer as any other file — see
[AI and files](files-and-transfers.md#ai-and-files).

## See also

- [Files and transfers](files-and-transfers.md) — the explorer, the editor and transfer
  history
- [Connection types](../connections/connection-types.md) — all ten types, and which open
  a terminal
- [Connection fields](../reference/connection-fields.md) — every field, per connection
  type
- [Credentials](../connections/credentials.md) — how the secret access key is stored
