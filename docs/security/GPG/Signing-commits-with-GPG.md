# Signing commits with GPG
You just need a GPG key. Please see [Generating a GPG key](Generating-a-GPG-key.md) for further details.

```
git config --global gpg.program gpg2 # Set the binary to use
git config --global user.signingkey IDCLAU # Set the user GPG key ID
git config --global commit.gpgsign true # Always sign the commits
```

In order to manually sign a commit:
```
git commit -S -m "Commit message"
```
