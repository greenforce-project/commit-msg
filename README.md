# Gerrit Change-Id Hooks

A minimally modified Gerrit `commit-msg` hook that automatically adds a `Change-Id` footer to Git commit messages.

This repository also includes a `prepare-commit-msg` hook to support commits created with `git revert`.

## Included Hooks

```text
commit-msg
prepare-commit-msg
```

### `commit-msg`

Generates and inserts a Gerrit `Change-Id` into the commit message footer.

### `prepare-commit-msg`

Detects commit messages created by `git revert` and runs the `commit-msg` hook before the revert commit is finalized.

## Installation

Run the following commands from the root directory of your Git repository:

```bash
mkdir -p .git/hooks

curl -fsSL \
  https://raw.githubusercontent.com/greenforce-project/commit-msg/main/commit-msg \
  -o .git/hooks/commit-msg

curl -fsSL \
  https://raw.githubusercontent.com/greenforce-project/commit-msg/main/prepare-commit-msg \
  -o .git/hooks/prepare-commit-msg

chmod +x .git/hooks/commit-msg
chmod +x .git/hooks/prepare-commit-msg
```

Verify the installation:

```bash
ls -l .git/hooks/commit-msg .git/hooks/prepare-commit-msg
```

Validate the shell syntax:

```bash
sh -n .git/hooks/commit-msg
sh -n .git/hooks/prepare-commit-msg
```

No output means the syntax is valid.

## Usage

After installation, use Git normally.

### How To Commit

```bash
git add .
git commit -m "kernel: update scheduler configuration"
```

Check the generated commit message:

```bash
git show -s --format=%B HEAD
```

Expected result:

```text
kernel: update scheduler configuration

Change-Id: Ixxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## Amend an Existing Commit

To add a missing `Change-Id` to the latest commit:

```bash
git commit --amend --no-edit
```

The hook preserves the existing commit message and adds a new `Change-Id` if one does not already exist.

Check the result:

```bash
git show -s --format=%B HEAD
```

## Configuration

To always generate a `Change-Id`, including for fixup and squash commits:

```bash
git config gerrit.createChangeId always
```

Apply the setting globally:

```bash
git config --global gerrit.createChangeId always
```

Check the current setting:

```bash
git config --get gerrit.createChangeId
```

Expected output:

```text
always
```

To disable automatic `Change-Id` generation for the current repository:

```bash
git config gerrit.createChangeId false
```

Remove the local configuration:

```bash
git config --unset gerrit.createChangeId
```

## Updating the Hooks

Run these commands from the root of the target repository:

```bash
curl -fsSL \
  https://raw.githubusercontent.com/greenforce-project/commit-msg/main/commit-msg \
  -o .git/hooks/commit-msg

curl -fsSL \
  https://raw.githubusercontent.com/greenforce-project/commit-msg/main/prepare-commit-msg \
  -o .git/hooks/prepare-commit-msg

chmod +x .git/hooks/commit-msg
chmod +x .git/hooks/prepare-commit-msg
```

## Troubleshooting

### `Change-Id` Is Not Generated

Check whether the hook exists:

```bash
ls -l .git/hooks/commit-msg
```

Make sure it is executable:

```bash
chmod +x .git/hooks/commit-msg
```

Check whether automatic generation is disabled:

```bash
git config --get gerrit.createChangeId
```

If the result is `false`, enable it:

```bash
git config gerrit.createChangeId always
```

### Revert Commit Has No `Change-Id`

Check whether `prepare-commit-msg` is installed:

```bash
ls -l .git/hooks/prepare-commit-msg
```

Make it executable:

```bash
chmod +x .git/hooks/prepare-commit-msg
```

For an already-created revert commit:

```bash
git commit --amend --no-edit
```

### Hook Syntax Error

Validate both hook files:

```bash
sh -n .git/hooks/commit-msg
sh -n .git/hooks/prepare-commit-msg
```

### Existing Hook Files

Back up existing hooks before installation:

```bash
cp .git/hooks/commit-msg .git/hooks/commit-msg.backup 2>/dev/null || true
cp .git/hooks/prepare-commit-msg .git/hooks/prepare-commit-msg.backup 2>/dev/null || true
```

## Uninstallation

Remove both hooks:

```bash
rm -f .git/hooks/commit-msg
rm -f .git/hooks/prepare-commit-msg
```

Remove the local Gerrit configuration if needed:

```bash
git config --unset gerrit.createChangeId
```

## Notes

- Git hooks are stored inside `.git/hooks` and are not committed with the target project repository.
- Each developer must install the hooks in their local clone.
- Existing `Change-Id` footers are preserved.
- A Gerrit `Change-Id` is different from a Git commit hash.
- Amending a commit preserves its existing `Change-Id`.
