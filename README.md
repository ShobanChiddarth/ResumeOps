# ResumeOps

CI/CD for your Resume.

- Store your Resumes in plain text files (LaTex files, with .tex extension)
- Track and version control them in git
- Maintain multiple resumes at once
- Automatically generate pdf files on `git push` (written to a local directory in the host where the self hosted GitHub Actions runner is running, as well as committed and tracked in another branch `pdfs`)
- Maintain the same directory tree structure in the source (.tex file branch), in the output as well as the backup branch

## Setup

1. Fork this repository into a private repository
2. Set up a self hosted GitHub Actions runner for that in your laptop
3. Change line 85 in `.github/workflows/build.yaml` to the directory where you want the pdfs to be saved locally

## Usage

1. Create .tex files in the `main` branch, using whatever directory structure you want
2. Stash it, commit it, push it
3. Automatically get the pdfs in the output repo you specified
4. If you want to retrieve an old version of the resume, see the `pdfs` branch

