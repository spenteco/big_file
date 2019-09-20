## big_file

A repo for testing Git Large File Storage (LFS).

## Process

**Created this repo**, and cloned it to my workstation.

**Followed the instructions** at https://git-lfs.github.com/  The instructions say to run a command like

  git lfs track "*.psd"

Instead, I did:

  git lfs track "big.xml"

**Created test files**, one big and one little XML file.

**Added, commit, push**.

## Results?

It works sort of OK.  The usual git workflow works.  But, like Craig says, it breaks the diff.  I updated my "big.xml" and "little.xml" files and checked them in.  The diff for little.xml looks like I would expect it to:

<pre>
$ git diff master~1:little.xml little.xml

  diff --git a/little.xml b/little.xml
  index e529ccc..97f32a1 100644
  --- a/little.xml
  +++ b/little.xml
  @@ -1,5 +1,10 @@
   &lt;?xml version="1.0" encoding="UTF-8"?&gt;
  +
  +
  +CHANGED THE FILE
  +
   &lt;?xml-model href="http://schemata.earlyprint.org/schemata/tei_earlyprint.rng" type="application/xml" schematypens="http://relaxng.org/ns/structure/1.0"?&gt;
  +
   &lt;TEI xmlns="http://www.tei-c.org/ns/1.0" xml:id="A30790"&gt;
    &lt;teiHeader&gt;
     &lt;fileDesc&gt;
</pre>

But the diff for big.xml is completely broken:

<pre>
$ git diff master~1:big.xml big.xml

  diff --git a/big.xml b/big.xml
  index cd4d754..8463a15 100644
  --- a/big.xml
  +++ b/big.xml
  @@ -1,3 +1,3 @@
   version https://git-lfs.github.com/spec/v1
  -oid sha256:4d1947a0a89de7f9dd07ef212332c6e433e3c1e4c2a2fef255d9fca7d143515f
  -size 242220685
  +oid sha256:f1d775b6363115b68d4d639727aab0876fe2f20c9f715e1b352584bb1c9d9b39
  +size 242220709
</pre>

Apparently, this has been a thing since forever.  See https://github.com/git-lfs/git-lfs/issues/440  One of the commenters offers a script (git-lfs-diff.sh, which I've added to this repo) for getting a diff for an LFS file.  It's a sligth hassle.  First, you have to figure out what commit versions you want to diff:

<pre>
$ git log big.xml

  commit d68d3ade7930ca757276b236f08d11c974e226dc
  Author: Stephen Pentecost <spentecost@email.wustl.edu>
  Date:   Fri Sep 20 10:15:03 2019 -0500
  
      updating XML
  
  commit 9f43d8d46414d304e1044b84e2684e3d390f3aa4
  Author: Stephen Pentecost <spentecost@email.wustl.edu>
  Date:   Fri Sep 20 10:11:27 2019 -0500
  
      adding files
</pre>

Then, you use the ungainly commit ID's to run the diff script:

<pre>
$ bash git-lfs-diff.sh d68d3ade7930ca757276b236f08d11c974e226dc 9f43d8d46414d304e1044b84e2684e3d390f3aa4 big.xml 

  --- .git/lfs/objects/f1/d7/f1d775b6363115b68d4d639727aab0876fe2f20c9f715e1b352584bb1c9d9b39	2019-09-20 10:15:01.117618592 -0500
  +++ .git/lfs/objects/4d/19/4d1947a0a89de7f9dd07ef212332c6e433e3c1e4c2a2fef255d9fca7d143515f	2019-09-20 10:09:43.604131213 -0500
  @@ -1,8 +1,4 @@
   &lt;?xml version="1.0" encoding="UTF-8"?>
  -
  -
  -CHANGED THE BIG FILE
  -
   &lt;?xml-model href="http://schemata.earlyprint.org/schemata/tei_earlyprint.rng" type="application/xml" schematypens="http://relaxng.org/ns/structure/1.0"?&gt;
   &lt;TEI xmlns="http://www.tei-c.org/ns/1.0" xml:id="A30905"&gt;
    &lt;teiHeader&gt;
</pre>


## Bottom line?

It's really a question of which inconvenience do we want to live with . . . this would, I suppose, be tolerable.


