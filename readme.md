# S3 storage system.
## Values: 
### Durability > Availability & Scalability
![high_level_s3](https://github.com/stormspirit03/portfolio/assets/53505985/4c1e8dde-7c08-4f3a-9bd3-14fa19970db7)

## Why this problem statement:
 I wanted to do project which involved in-depth understanding of distrubuted DB and scaling.<br> Also this problemstatement covers core backend skills such as Queue, Load-balancing,<br> Replication,  Micro-service architecture, Multi-Tencnacy.
 
## DB:
* DB is has 3 major sections, 1. Raw storage 2. Meta-data 3. Cache
* I will be using MongoDB for raw storage as-well for meta-data.
  - As Mongo supports large file upload, also, it is flexible for Meta-data.
  - I want to keep flat storage. key-value pair will reduce complexity and search will be faster.
  - Mongo has built-in support for horizontal scaling, replication , sharding.

## SERVERs: 
* There will be Load-balancer, API server, Meta-data server, DB servers in a mmicro-service architecture.
* API server will communicate with Meta-Data server for which DB server to search for download aswell upload.
* Queue is used to de-couple the api server from DB server. Read and write are also intended to keep separate which is subject to
  complition of core developement first.

## Utility:
* AES 128 is planned for ecrytion ( security ), md5 for checksum (integrity). 
* File organization is managed using prefix ( key-value ).
* Meta-data will contain the version info.

## Meta-Data structure ( subject to modification )
  - user_id : uploader id.
  - Tenant_id: for multi tenant( collection per tenant/ company).
  - prefix: path address followed by file name. ( logical organization of files and folders )
  - name : name of the file.
  - UUID: hash, to decide which DB server holds the actual file.
  - size: size of the file.
  - type: file type.
  - version: version details.
  - hash: for checksum and deduplication.
  - created_at: time of creataion.
    
## Stack:
- Mongo DB: will allow easy replication, sharding, flat storage.
- Queue: For now RabitQ due to ease of implementation and if needed can be upgraded to kafka.
  

## Main API's : 
1. User Authentication:
Register:

```
POST /api/auth/register
```
Parameters: username, password.

Response: User registration confirmation

Login:
```
POST /api/auth/login
```
Parameters: username, password.

Response: User login confirmation, token.

2. File Operations:
Upload File:

```
POST /api/files/upload
```
Parameters: file, user token.

Response: File upload confirmation

Download File:

```
GET /api/files/:fileId/download
```
Parameters: fileId, user token.

Response: File download stream.

Delete File:

```
DELETE /api/files/:fileId
```
Parameters: fileId, user token.

Response: File deletion confirmation.

3. File Organization (In a Flat Storage Structure):
   
Create Logical Folder (Virtual Representation):
```
POST /api/folders/create
```
Parameters: folderName, user token.

Response: Folder creation confirmation.

4. File Listing and Search:
List All Files:

```
GET /api/files
```
Parameters: user token.

Response: List of all files.

Search Files:

```
GET /api/files/search
```
Parameters: query, user token.

Response: Matching files.

5. Access Control:

Set File Access:
```
PUT /api/files/:fileId/access
```
Parameters: fileId, accessType (public, private, shared), user token.

Response: Access control confirmation.

6. File Versioning:
   
Upload New Version:

```
POST /api/files/:fileId/version
```
Parameters: fileId, newFile, user token.

Response: Version upload confirmation.
Rollback to Previous Version:

```
PUT /api/files/:fileId/version/:versionId
```
Parameters: fileId, versionId, user token.

Response: Rollback confirmation.

7. Metadata Management:
   
Update File Metadata:
```
PUT /api/files/:fileId/metadata
```
Parameters: fileId, metadata (tags, description), user token.

Response: Metadata update confirmation

8. File Deduplication:
In the context of deduplication:
Deduplicate File:
When a new file is uploaded, generate a hash (SHA) of its content.
Compare the hash with existing hashes in your storage.
If a file with the same hash already exists, it's a duplicate, and you can link to the existing copy rather than storing it again.

10. Usage Analytics:
Storage Usage Analytics:

```
GET /api/analytics/storage
```
Parameters: user token.

Response: Storage usage analytics.
File Type Analytics:

```
GET /api/analytics/file-types
```
Parameters: user token.

Response: File type analytics.





    



