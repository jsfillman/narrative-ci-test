## Testing jsfillman/narrative-ci-test 

- [x] Fix existing SHA check (skip if exists)
- [x] Try with hard-coded appnames for prod (and tag_env?)

#### Tagged `next` after `appdev` built
```
(For Appdev)
MY_SHA: c10b825
PULL_NUMBER: pull_1
APP_VERSION_NUM: 
TEST_APP: jsfillman/narrative-ci-test/narrative-ci-test-testing
MY_DEV_APP: jsfillman/narrative-ci-test/narrative-ci-test-appdev
MY_NEXT_APP: jsfillman/narrative-ci-test/narrative-ci-test-next
MY_CI_APP: jsfillman/narrative-ci-test/narrative-ci-test-ci
MY_PROD_APP: jsfillman/narrative-ci-test/narrative-ci-test-prod
MY_USER: jsfillman
MY_GPR: narrative-ci-test
ISDEV: 0
ISNEXT: 0
ISCI: 0
ISPROD: 0
```

```
(For Next)
MY_SHA: c10b825
PULL_NUMBER: pull_1
APP_VERSION_NUM: 
TEST_APP: jsfillman/narrative-ci-test/narrative-ci-test-testing
MY_DEV_APP: jsfillman/narrative-ci-test/narrative-ci-test-appdev
MY_NEXT_APP: jsfillman/narrative-ci-test/narrative-ci-test-next
MY_CI_APP: jsfillman/narrative-ci-test/narrative-ci-test-ci
MY_PROD_APP: jsfillman/narrative-ci-test/narrative-ci-test-prod
MY_USER: jsfillman
MY_GPR: narrative-ci-test
ISDEV: 0
ISNEXT: 0
ISCI: 0
ISPROD: 0
```

### Untagged `appdev` and started again (next still tagged)

```
MY_SHA: c10b825
PULL_NUMBER: pull_1
APP_VERSION_NUM: 
TEST_APP: jsfillman/narrative-ci-test/narrative-ci-test-testing
MY_DEV_APP: jsfillman/narrative-ci-test/narrative-ci-test-appdev
MY_NEXT_APP: jsfillman/narrative-ci-test/narrative-ci-test-next
MY_CI_APP: jsfillman/narrative-ci-test/narrative-ci-test-ci
MY_PROD_APP: jsfillman/narrative-ci-test/narrative-ci-test-prod
MY_USER: jsfillman
MY_GPR: narrative-ci-test
ISDEV: 0
ISNEXT: 0
ISCI: 0
ISPROD: 0
```

- Ok, so obviously not checking for SHA tags in the GPR. Let's fix that
- Testing from a new PR, to see if initial build creates SHA

### SHA tracing

- Initial build tags with SHA `sha-24e3f0d`
- Looks like `env.ISDEV < 1` portion isn't working
- Replaced with new $ISDEV test (unfortunately this method requires knowing the package's URL)

- Testing:
	- testing: sha-f796e09
	- appdev: sha-f796e09
	- next: sha-f796e09
- Tried untagging & retagging appdev -- No $ISDEV check?

### PR 5

- Fixed export string for $ISDEV
- Testing sha-7a2e38f

Error
```
 Error response from daemon: manifest for docker.pkg.github.com/jsfillman/narrative-ci-test/narrative-ci-test-testing:sha-e7f0e5f not found: manifest unknown: Docker image reference narrative-ci-test-testing:sha-e7f0e5f not found under repo "jsfillman"
```

### PR 6

- Found incorrect URL for appdev
- Fixing and trying again

### PR 7

- SHA: sha-2d9dfd1
- Still getting ISDEV>0 false
- Wrong URL & didn't have the `wc` to count occurance

### PR 8

- Working for appdev!!

### PR 9

- SHA: sha-5122080
- Added URLs for next & ci
- Tagged appdev first
- Isn't building, due to the `wc` command always returning 1 

### PR 10

- SHA: sha-edcc269
- Fixed issue with `wc` above
- Testing appdev... works
- Add next, remove appdev... works, only next built
- Add ci... works, only ci built
- Re-add appdev... works, no builds
- Remove next & ci... ok
- Re-add next & ci... ok

- Made new commit to PR (branch `merge10`)
- New SHA: sha-c11e5c5
- Removed & re-added appdev tag... works!
	- Caveat: If `next` and `ci` are still set, removing/adding will cause next & ci to build as well
	- This is likely desired behavior, since any commits to an existing PR branch would likely be bug fixes
	- Each image/package has a unique SHA tag, so older builds to a specific branch/PR can still be referenced
- Removed & readded next & ci... 

### Merge PR 10
- Confirmed the `tag_prod` workflow still adds the relevant $APP-prod label