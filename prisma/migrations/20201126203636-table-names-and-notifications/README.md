# Migration `20201126203636-table-names-and-notifications`

This migration has been generated by Diego Fernandes at 11/26/2020, 5:36:36 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
ALTER TABLE "Tweet" DROP CONSTRAINT "Tweet_parentId_fkey"

ALTER TABLE "Tweet" DROP CONSTRAINT "Tweet_userId_fkey"

ALTER TABLE "TweetLikes" DROP CONSTRAINT "TweetLikes_tweetId_fkey"

ALTER TABLE "TweetLikes" DROP CONSTRAINT "TweetLikes_userId_fkey"

ALTER TABLE "TweetMentions" DROP CONSTRAINT "TweetMentions_tweetId_fkey"

ALTER TABLE "TweetMentions" DROP CONSTRAINT "TweetMentions_userId_fkey"

ALTER TABLE "UserFollows" DROP CONSTRAINT "UserFollows_targetId_fkey"

ALTER TABLE "UserFollows" DROP CONSTRAINT "UserFollows_userId_fkey"

CREATE TABLE "users" (
    "id" TEXT NOT NULL,
    "email" TEXT NOT NULL,
    "name" TEXT,
    "password" TEXT NOT NULL,

    PRIMARY KEY ("id")
)

CREATE TABLE "tweets" (
    "id" TEXT NOT NULL,
    "userId" TEXT NOT NULL,
    "content" TEXT NOT NULL,
    "parentId" TEXT NOT NULL,

    PRIMARY KEY ("id")
)

CREATE TABLE "user_follows" (
    "id" TEXT NOT NULL,
    "userId" TEXT NOT NULL,
    "targetId" TEXT NOT NULL,

    PRIMARY KEY ("id")
)

CREATE TABLE "tweet_likes" (
    "id" TEXT NOT NULL,
    "tweetId" TEXT NOT NULL,
    "userId" TEXT NOT NULL,

    PRIMARY KEY ("id")
)

CREATE TABLE "notifications" (
    "id" TEXT NOT NULL,
    "content" TEXT NOT NULL,
    "userId" TEXT NOT NULL,

    PRIMARY KEY ("id")
)

DROP TABLE "Tweet"

DROP TABLE "TweetLikes"

DROP TABLE "TweetMentions"

DROP TABLE "User"

DROP TABLE "UserFollows"

CREATE UNIQUE INDEX "users.email_unique" ON "users"("email")

ALTER TABLE "tweets" ADD FOREIGN KEY("userId")REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "tweets" ADD FOREIGN KEY("parentId")REFERENCES "tweets"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "user_follows" ADD FOREIGN KEY("userId")REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "user_follows" ADD FOREIGN KEY("targetId")REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "tweet_likes" ADD FOREIGN KEY("tweetId")REFERENCES "tweets"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "tweet_likes" ADD FOREIGN KEY("userId")REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE "notifications" ADD FOREIGN KEY("userId")REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration 20201126203321-tweet-parent..20201126203636-table-names-and-notifications
--- datamodel.dml
+++ datamodel.dml
@@ -2,58 +2,66 @@
 // learn more about it in the docs: https://pris.ly/d/prisma-schema
 datasource db {
   provider = "postgresql"
-  url = "***"
+  url = "***"
 }
 generator client {
   provider = "prisma-client-js"
 }
 model User {
-  id          String          @id @default(uuid())
-  email       String          @unique
-  name        String?
-  password    String
-  tweets      Tweet[]
-  mentionedIn TweetMentions[]
-  likedTweets TweetLikes[]
-  following   UserFollows[]   @relation("UserFollowsUser")
-  followers   UserFollows[]   @relation("UserFollowsTarget")
+  id            String          @id @default(uuid())
+  email         String          @unique
+  name          String?
+  password      String
+  tweets        Tweet[]
+  likedTweets   TweetLikes[]
+  following     UserFollows[]   @relation("UserFollowsUser")
+  followers     UserFollows[]   @relation("UserFollowsTarget")
+  notifications Notifications[]
+
+  @@map("users")
 }
 model Tweet {
-  id           String          @id @default(uuid())
-  from         User            @relation(fields: [userId], references: [id])
+  id           String       @id @default(uuid())
+  from         User         @relation(fields: [userId], references: [id])
   userId       String
   content      String
-  responseFrom Tweet           @relation("TweetComments", fields: [parentId], references: [id])
-  comments     Tweet[]         @relation("TweetComments")
+  responseFrom Tweet        @relation("TweetComments", fields: [parentId], references: [id])
+  comments     Tweet[]      @relation("TweetComments")
   parentId     String
-  mentions     TweetMentions[]
   likes        TweetLikes[]
+
+  @@map("tweets")
 }
 model UserFollows {
   id       String @id @default(uuid())
   user     User   @relation("UserFollowsUser", fields: [userId], references: [id])
   userId   String
   target   User   @relation("UserFollowsTarget", fields: [targetId], references: [id])
   targetId String
+
+  @@map("user_follows")
 }
-model TweetMentions {
+model TweetLikes {
   id      String @id @default(uuid())
   tweet   Tweet  @relation(fields: [tweetId], references: [id])
   user    User   @relation(fields: [userId], references: [id])
   tweetId String
   userId  String
+
+  @@map("tweet_likes")
 }
-model TweetLikes {
+model Notifications {
   id      String @id @default(uuid())
-  tweet   Tweet  @relation(fields: [tweetId], references: [id])
+  content String
   user    User   @relation(fields: [userId], references: [id])
-  tweetId String
   userId  String
+
+  @@map("notifications")
 }
```


