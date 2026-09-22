# LongMarchAssetsLFS

Models, textures, fonts, and binary data are stored with
[Git LFS](https://git-lfs.com/). Scene descriptions, shaders, and other small
text files remain in Git.

## Downloading assets

Install Git LFS and run `git lfs install` before cloning. For this repository
as a submodule of LongMarch, run from the LongMarch root:

```bash
git submodule update --init --recursive -- assets
git -C assets lfs pull
```

For a standalone clone, run `git lfs pull` inside this repository. To delay
downloads in Bash/zsh, prefix the clone command with `GIT_LFS_SKIP_SMUDGE=1`.
Then download only the required paths, for example:

```bash
git lfs pull --include="textures/**,meshes/**" --exclude=""
```

Omit `--include` to download all assets for the checked-out commit. Include any
additional paths required by your scene. Demos need the actual asset contents,
not the small LFS pointer files.

## Contributing

The patterns in `.gitattributes` select files stored in LFS. Add a new pattern
with `git lfs track "*.extension"` when introducing another large asset format,
and commit `.gitattributes` along with the assets. Use normal `git add`,
`git commit`, and `git push`; the LFS pre-push hook uploads the file contents
before publishing their Git pointers.

Check `git lfs status` before committing and run `git lfs fsck` after committing.
Do not skip the LFS pre-push hook. When updating LongMarch's submodule pointer,
publish this repository's commit and LFS objects first.

This repository starts with fresh Git history and stores large assets in LFS
from its first commit. The original LongMarchAssets repository is preserved
for older LongMarch revisions.

## Source snapshot

Imported from `LazyJazzDev/LongMarchAssets` branch `blender-align`, commit
`04d673a1307304fb7e94829b0d0fd0bd279df00b`. Original scene documentation follows.

# LongMarchAssets
