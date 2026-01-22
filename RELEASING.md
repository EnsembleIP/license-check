# Releasing `license-check`

GitHub Actions references use Git refs. For example, `uses: ensembleip/license-check@v1` requires a tag (or branch) named `v1` in this repository.

## Recommended approach (major tag + semver tags)

Create a semver tag for the release, and move the major tag to the same commit:

```bash
git checkout master
git pull

# Create an immutable semver tag for this release
git tag -a v1.0.0 -m "license-check v1.0.0"
git push origin v1.0.0

# Create (or move) the floating major tag
git tag -f v1
git push -f origin v1
```

Now downstream repos can use:

- `ensembleip/license-check@v1` (tracks latest v1)
- `ensembleip/license-check@v1.0.0` (fixed version)
- `ensembleip/license-check@<commit SHA>` (most secure pin)

## Testing before release

Before tagging a release, test the action in a real PR:

1. Create a test repository or use an existing one with Python dependencies
2. Reference the action using a branch or commit SHA:
   ```yaml
   uses: ensembleip/license-check@main
   # or
   uses: ensembleip/license-check@abc123def456
   ```
3. Create a test PR and verify that:
   - The license check runs successfully
   - A comment is posted to the PR with license results
   - Updating the PR updates the existing comment (no duplicates)
   - Custom input parameters work correctly
   - Skip dependencies are properly excluded

## Version conventions

- **v1.x.x**: Production-ready releases
- **v1**: Floating tag that always points to the latest v1.x.x release
- Use semantic versioning for all releases:
  - **MAJOR**: Breaking changes to inputs or behavior
  - **MINOR**: New features, backward compatible
  - **PATCH**: Bug fixes, backward compatible

## Release checklist

Before creating a new release:

- [ ] Update version in documentation examples if needed
- [ ] Test the action in a real repository
- [ ] Update CHANGELOG.md (if present) with release notes
- [ ] Ensure all CI checks pass
- [ ] Create git tag and push
- [ ] Verify the action works with the new tag reference
