# About the test suite

This test suite exists to test the behaviour of project updates
for AWX jobs with branch overrides set (or not set)

There are three branches used for testing
* project-default - used to ensure that job-default is respected
  (should never actually be hit)
* job-default - used when no branch override is set
* override - used to test branch overrides
* main - should always be ahead of default and override, really
  exists to check that the project update isn't just pulling in
  HEAD

# Running the test suite

* Ensure that a fresh, throw-away AWX is running
  (e.g. `make docker-compose` from the awx repo)

* Install the AWX collection
  ```
  ansible-galaxy collection install -p . awx.awx
  ```

## Part 1 (project branch unset)

Run the setup playbook with a working admin password (docker-compose
displays it at start up)

```
ansible-playbook awx-setup.yml -e admin_password=ADMIN_PASSWORD
```

### Test 1

Project branch unset, use job template default branch
```
ansible-playbook test-suite/job-default.yml
```

Result:
  * FAIL: Updates inventory to HEAD but executes on job-default branch

### Test 2

Project branch unset, override job template default branch
```
ansible-playbook test-suite/job-override.yml
```

Result:
  * FAIL: Updates inventory to job-default but executes on override branch


## Part 2 (project branch set to same as job template branch)

Run the setup playbook with a working admin password (docker-compose
displays it at start up)

```
ansible-playbook awx-setup.yml -e admin_password=ADMIN_PASSWORD -e project_branch=job-default
```

### Test 3

Project branch set, use job template default branch
```
ansible-playbook test-suite/job-default.yml
```

Result:
  * PASS

### Test 4

Project branch set, override job template default branch
```
ansible-playbook test-suite/job-override.yml
```

Result:
  * FAIL: Updates inventory to job-default but executes on override branch
