Release checklist:

Tagging:
 - [ ] Write release notes in NEWS. Get them reviewed and merged
     - [ ] If doing a branched release, also include a PR to merge the NEWS changes into main
 - [ ] Ensure your local copy is up to date with the upstream main branch (`git@github.com:coreos/butane.git`)
 - [ ] Ensure your working directory is clean (`git clean -fdx`)
 - [ ] Ensure you can sign commits and any yubikeys/smartcards are plugged in
 - [ ] Run `./tag_release.sh <vX.Y.z> <git commit hash>`
 - [ ] Push that tag to GitHub

Packaging:
 - [ ] Update the Butane spec file in [Fedora](https://src.fedoraproject.org/rpms/butane):
   - Bump the `Version`
   - Switch the `Release` back to `1%{?dist}`
   - Remove any patches obsoleted by the new release
   - Run `go-mods-to-bundled-provides.py | sort` while inside of the butane directory you ran `./tag_release` from & copy output into spec file in `# Main package provides` section
   - Update change log
 - [ ] Run `spectool -g -S butane.spec`
 - [ ] Run `kinit your_fas_account@FEDORAPROJECT.ORG`
 - [ ] Run `fedpkg new-sources tarball-name`
 - [ ] PR the changes in [Fedora](https://src.fedoraproject.org/rpms/butane)
 - [ ] Once the PR merges to rawhide, merge rawhide into the other relevant branches (e.g. f30) then push those
 - [ ] On each of those branches run `fedpkg build`
 - [ ] Once the builds have finished, submit them to [bodhi](https://bodhi.fedoraproject.org/updates/new), filling in:
   - `butane` for `Packages`
   - Selecting the build(s) that just completed, except for the rawhide one (which gets submitted automatically)
   - Writing brief release notes like "New upstream release. See release notes at `link to NEWS on GH tag`"
   - Leave `Update name` blank
   - `Type`, `Severity` and `Suggestion` can be left as `unspecified` unless it is a security release. In that case select `security` which the appropriate severity.
   - `Stable karma` and `Unstable` karma can be set to `2` and `-1`, respectively.

GitHub release:
 - [ ] Wait until the Bodhi update shows "Signed :heavy_check_mark:" in the Metadata box.
 - [ ] [File a releng ticket](https://pagure.io/releng/new_issue) based on [prior signing tickets](https://pagure.io/releng/issue/10158).
   - [ ] Update the script and test it locally by dropping the `sigul` lines.
     - [ ] If a new Fedora release has gone stable, update the signing key in the script to use the new Fedora signing key [found here](https://getfedora.org/security).
 - [ ] Ping `mboddu` in Libera.Chat `#fedora-coreos`, linking to the ticket
 - [ ] Wait for the ticket to be closed
 - [ ] Download the artifacts and signatures
 - [ ] Verify the signatures
 - [ ] Find the new tag in the [GitHub tag list](https://github.com/coreos/butane/tags) and click the triple dots menu, and create a draft release for it.
 - [ ] Upload all the release artifacts and their signatures. Copy and paste the release notes from NEWS here as well.
   - [ ] If the signing key has changed, note the change in the GitHub release notes as done [here](https://github.com/coreos/butane/releases/tag/v0.12.0).
 - [ ] Publish the release

Quay release:
 - [ ] Visit the [Quay tags page](https://quay.io/repository/coreos/butane?tab=tags) and wait for a versioned tag to appear
 - [ ] Click the gear next to the tag, select "Add New Tag", enter `release`, and confirm
 - [ ] Visit the [Quay tags page](https://quay.io/repository/coreos/fcct?tab=tags) for the legacy FCCT repo and wait for a versioned tag to appear
 - [ ] Click the gear next to the tag, select "Add New Tag", enter `release`, and confirm

Housekeeping:
 - Update Butane package for the current RHCOS development release
   - [ ] Update the dist-git repo, following the instructions above
   - [ ] PR the changes
   - [ ] When the PR lands, build the package
 - [ ] File ticket similar to [this one](https://issues.redhat.com/browse/ART-3043) to sync the new version to mirror.openshift.com
 - [ ] Ask bgilbert to update the [MacPorts package](https://github.com/macports/macports-ports/tree/master/sysutils/butane)
