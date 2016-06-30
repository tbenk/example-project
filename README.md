= Service tar_scm

== Additional Parameters

=== Parameter export

tar_scm will save the version and the revision information in the specified file. This file will be available for other services.

Example:

----
{
    "version": "0.1",
    "release": "gcc62c54.<CI_CNT>.<B_CNT>"
}
----

=== Parameter release

Specify the release to be used in the tarball.  Defaults to automatically  detected value formatted by the releaseformat parameter. The release will not be used in the generated filename, but it will be exported if the _export_ parameter was given.

==== Release Tag

The default Release tag uses two magic strings:

. CI_CNT <- number of commits

. B_CNT <- number of rebuilds (when the rebuild is triggerd)

The default setting is

----
Release: <CI_CNT>.<B_CNT>
----

The custom release string and the magic strings must be delimited by a ".".

The magic strings must not be enclosed in angle brackets when specified in the
_service file!

Either CI_CNT or B_CNT must be part of the _release_ parameter!

=== Parameter releaseformat

Auto-generate release from checked out source using this format
string.  This parameter is used if the _release_ parameter is not
specified. The release will not be used in the generated filename, but it will be exported if the _export_ parameter was given.

Either CI_CNT or B_CNT must be part of the _releaseformat_ parameter!


For git, the value is passed to git log --date=short --pretty=format:... for the topmost commit, and the output from git is cleaned up to remove some unhelpful characters.  Here are some useful examples of strings which are expanded, see the git-log documentation for more.

----
      %ct               Commit time as a UNIX timestamp, e.g. 1384855778.
                        This is the default.

      %at               Author time as a UNIX timestamp, e.g. 1384855776.

      %cd               Commit date in YYYYMMDD format, e.g. 20131119

      %ad               Author date in YYYYMMDD format, e.g. 20131119

      %h                Abbreviated hash, e.g. cc62c54

      @PARENT_TAG@      The first tag that is reachable, e.g. v0.2.3

      @TAG_OFFSET@      The commit count since @PARENT_TAG@, e.g. 9

----

For hg, the value is passed to hg log --template=....  See the hg documentation for more information.  The default is '{rev}'

For bzr and svn, '%r' is expanded to the revision, and is the default.

=== Parameter releaseprefix

Specify a base release as prefix.

= Service tar_scm

== Additional Parameters

=== Parameter import

Use version and release string given in json file, do not detect it from source files.

= Usage Examples

== Example 1

----
<services>
  <service name="tar_scm">
    <param name="scm">git</param>
    <param name="url">https://github.com/tbenk/example-project.git</param>
    <param name="version">0.1</param>    
    <param name="releaseformat">g%h.CI_CNT.B_CNT</param>    
    <param name="filename">example-project</param>
    <param name="export">data.json</param>    
  </service>
  <service name="set_version">
    <param name="import">data.json</param>    
  </service>
</services>
----

In this example, the tar_scm service will

- generate a file named _example-project-0.1.tar_
- format the release string according to releaseformat
- export the version and the release to a file called _data.json_

----
{
    "version": "0.1",
    "release": "gcc62c54.<CI_CNT>.<B_CNT>"
}
----

The service set_version will

- read in the file _data.json_ and use the given values to replace the Service field and the Release field in the spec file

