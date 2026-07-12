# ResumeOps

CI/CD for your Resume.

- Store your Resumes in plain text files (LaTex files, with .tex extension)
- Track and version control them in git
- Maintain multiple resumes at once
- Automatically generate pdf files on `git push` (written to a local directory in the host where the self hosted GitHub Actions runner is running, as well as committed and tracked in another branch `pdfs`)
- Maintain the same directory tree structure in the source (.tex file branch), in the output as well as the backup branch

## Requirements

- Docker
- A self hosted GitHub Actions runner
- Git
- A GitHub repository

The workflow automatically pulls the required Tectonic container image, so no local LaTeX installation is required.

## Setup

1. Fork this repository into a private repository
2. Set up a self hosted GitHub Actions runner for that in your laptop
3. Change line 85 in `.github/workflows/build.yaml` to the directory where you want the pdfs to be saved locally

## Usage

1. Create .tex files in the `main` branch, using whatever directory structure you want
2. Stash it, commit it, push it
3. Automatically get the pdfs in the output repo you specified
4. If you want to retrieve an old version of the resume, see the `pdfs` branch

## GitHub Actions Workflow explanation

The workflow consists of four stages:

### 1. Checkout source

Checks out the latest commit from the `main` branch onto the self hosted runner.

```yaml
- uses: actions/checkout@v4
```

### 2. Build all PDFs

Searches the repository recursively for every `.tex` file and compiles each one inside the Tectonic Docker container. The generated PDF is written next to its corresponding source file.

```yaml
find . -type f -name '*.tex'
```

This means you can organize resumes however you like:

```
.
├── Software/
│   ├── resume.tex
│   └── internship.tex
├── Cybersecurity/
│   └── resume.tex
└── Networking/
    └── resume.tex
```

The resulting PDFs will have the same directory structure.

### 3. Publish PDFs to the `pdfs` branch

- All generated PDFs are first copied into a temporary directory.
- A fresh clone of the repository is then created in another temporary directory. If the `pdfs` branch already exists, it is checked out. Otherwise, the workflow creates it as an orphan branch.
- The contents of the branch are removed, the newly generated PDFs are copied into it, and the changes are committed and pushed.
- Only PDF files are stored in the `pdfs` branch, making it a versioned backup of every generated resume.

### 4. Mirror PDFs locally

Finally, every generated PDF is copied to the output directory specified in `build.yaml`.

For example, if your repository contains

```
Software/resume.tex
Cybersecurity/resume.tex
Networking/internship.tex
```

and `OUTPUT_DIR` is

```
/home/user/Documents/Resumes
```

the workflow produces

```
/home/user/Documents/Resumes/
├── Cloud/
│   └── resume.pdf
├── Cybersecurity/
│   └── resume.pdf
└── Networking/
    └── resume.pdf
```

The directory structure always matches the source repository.
