# packup

A command-line tool for packing multiple text files into a single file and unpacking them later while preserving directory structure. Particularly useful for sharing codebases with Large Language Models (LLMs) while managing context windows and respecting `.gitignore` rules.

## Features

- Pack multiple text files into a single file
- Unpack files while preserving directory structure
- Automatic text file detection and binary file exclusion
- Respects `.gitignore` rules when available
- Custom ignore patterns support
- Token counting for LLM context management
- Visual directory tree output
- Color-coded status indicators

## LLM Usage

This tool is specifically designed to help when sharing codebases with LLMs:

- Automatically counts tokens (estimated at 4 chars per token)
- Skips files that would exceed typical LLM context windows (default 10k tokens)
- Preserves file structure information in a format LLMs can understand
- Excludes binary files and other content that wouldn't be useful to LLMs
- Creates a single, well-formatted file you can easily paste into LLM chats

### Using with LLMs

1. Pack your project:
```bash
packup pack ./my-project
```

2. Copy the contents of the generated `packed_files.txt`

3. Paste into your LLM chat with a prompt like:
```
I'm going to share my codebase with you. The files will be in this format:
BEGIN_FILE;./path/to/file
[file contents]
END_FILE

Please analyze these files:
[paste packed_files.txt content here]
```

### Recreating Files from LLM Output

If an LLM generates multiple files for you, you can ask it to format them using the packup format. Here's a prompt you can use:

```
Please provide the files in a concatenated format with BEGIN_FILE and END_FILE markers, using this structure:
BEGIN_FILE;./filepath
[content]
END_FILE

This format lets me automatically unpack the files using the packup tool.
```

Then save the LLM's response to a file and unpack it:
```bash
packup unpack llm_response.txt ./new-project
```

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/packup.git
cd packup

# Make the script executable
chmod +x packup

# Option 1: Install to /usr/local/bin (requires sudo)
sudo cp packup /usr/local/bin/

# Option 2: Install to ~/.local/bin
mkdir -p ~/.local/bin
cp packup ~/.local/bin/
```

Make sure your chosen installation directory is in your PATH.

## Usage

### Pack Files

Pack files from a directory:
```bash
packup pack [options] <directory> [output_file]
```

Options:
- `-v`: Verbose mode
- `-i pattern`: Ignore files matching pattern (can be used multiple times)
- `-h, --help`: Show help for pack command

Examples:
```bash
# Pack all files in current directory
packup pack ./

# Pack with custom output file
packup pack src/ output.txt

# Ignore specific patterns
packup pack -i '*.log' -i 'temp/*' src/

# Pack with verbose output
packup pack -v project/
```

### Unpack Files

Unpack files from a packed file:
```bash
packup unpack <input_file> [target_directory]
```

Examples:
```bash
# Unpack to current directory
packup unpack packed_files.txt

# Unpack to specific directory
packup unpack packed_files.txt output/
```

### Help

Show help information:
```bash
packup help        # Show general help
packup help pack   # Show pack command help
packup help unpack # Show unpack command help
packup -h          # Show help (alternative)
```

## File Handling

- Only text files are packed (detected automatically)
- Binary files are automatically excluded
- Files over 40,000 characters (~10,000 tokens) are skipped
- Git ignore rules are respected if available
- Hidden files (starting with .) are excluded

## Ignore Patterns

You can specify custom ignore patterns using the `-i` flag:

```bash
*.log           # Ignore all .log files
test/           # Ignore 'test' directory
temp/*          # Ignore contents of 'temp' directory
*.{tmp,bak}     # Ignore .tmp and .bak files
```

## Output Format

The packed file format is simple and human-readable:

```
BEGIN_FILE;./relative/path/to/file.txt
[file contents]
END_FILE
BEGIN_FILE;./another/file.js
[file contents]
END_FILE
```

## Visual Output

The tool provides a visual directory tree showing:
- ✓ Green check marks for included files
- ✗ Red X's for excluded files
- File token counts for included files
- Reasons for exclusion
- Directory structure in blue

Example output:
```
Using git ignore rules
File system structure:
├── ✓ README.md (250 tokens)
├── ✗ node_modules (git ignored)
├── ✓ package.json (329 tokens)
└── src
    ├── app
    │   └── ✓ index.ts (363 tokens)
    ├── components
    │   ├── ✓ Button.tsx (1047 tokens)
    │   └── ✗ logo.png (binary file)
    └── styles
        └── ✓ main.css (620 tokens)
```

## Use Cases

- Share multiple files with LLMs while respecting token limits
- Create single-file backups of text-based projects
- Bundle source code for sharing or review
- Archive text files while maintaining structure
- Create reproducible snapshots of text-only content

## License

MIT

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
