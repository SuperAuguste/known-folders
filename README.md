# Zig Special Folders Project

## Design Goals

- Minimal API surface
- Provide the user with an option to either obtain a directory handle or a path name
- Keep to folders that are available on all operating systems

## Resources

### *nix / XDG
- https://www.freedesktop.org/wiki/Software/xdg-user-dirs/
- https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html

### Windows
- https://docs.microsoft.com/en-us/previous-versions/windows/desktop/legacy/bb776911(v=vs.85)
- Vista or newer: SHGetKnownFolderPath
- Earlier: SHGetFolderPath

### MacOS
- https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html#//apple_ref/doc/uid/TP40010672-CH2-SW6

## Similar Packages
(for reference)

- https://docs.rs/directories/2.0.2/directories/index.html

## Open Questions
- how do we handle xdg dirs when wanted on mac?

## Folder List

| Folder                | Windows                  | xdg                        | MacOS |
|-----------------------|--------------------------|----------------------------|-----------|
| Home                  | FOLDERID_Profile         | HOME                       | HOME |
| Documents             | FOLDERID_Documents       | XDG_DOCUMENTS_DIR          | HOME/Documents |
| Pictures              | FOLDERID_Pictures        | XDG_PICTURES_DIR           | HOME/Pictures |
| Music                 | FOLDERID_Music           | XDG_MUSIC_DIR              | HOME/Music |
| Videos                | FOLDERID_Videos          | XDG_VIDEOS_DIR             | HOME/Movies |
| Templates             | FOLDERID_Templates       | XDG_TEMPLATES_DIR          | (N/A) |
| Desktop               | FOLDERID_Desktop         | XDG_DESKTOP_DIR            | HOME/Desktop |
| Downloads             | FOLDERID_Downloads       | XDG_DOWNLOAD_DIR           | HOME/Downloads |
| Public                | FOLDERID_Public          | XDG_PUBLICSHARE_DIR        | HOME/Public |
| Fonts                 | FOLDERID_Fonts           | XDG_DATA_HOME/fonts        | HOME/Library/Fonts |
| App Menu              | FOLDERID_StartMenu       | XDG_DATA_HOME/applications | |
| Cache                 | %LOCALAPPDATA%/Temp      | XDG_CACHE_HOME             | HOME/Library/Caches |
| Roaming Configuration | %APPDATA%	           | XDG_CONFIG_HOME            | HOME/Library/Preferences |
| Local Configuration   | %LOCALAPPDATA%           | XDG_CONFIG_HOME            | HOME/Library/Application Support |
| Data                  | %APPDATA%                | XDG_DATA_HOME              | HOME/Library/Application Support | 
| System Folder         | FOLDERID_System          | /                          | / |
| Runtime               | %LOCALAPPDATA%/Temp      | XDG_RUNTIME_DIR            | ? |

## API
```zig
pub const SpecialFolder = enum {
  home,
  documents,
  pictures,
  music,
  videos,
  templates,
  desktop,
  downloads,
  public,
  fonts,
  app_menu,
  cache,
  roaming_configuration,
  local_configuration,
  data,
  system_folder,
  runtime,
};

// Explicitly define possible errors to make it clearer what callers need to handle
pub const Error = error {
// TODO: fill this in
	OutOfMemory,
};

/// Returns a directory handle, or, if the folder does not exist, `null`.
pub fn open(allocator: *std.mem.Allocator, folder: SpecialFolder) Error!?std.fs.Dir;

/// Returns the path to the folder or, if the folder does not exist, `null`.
pub fn getPath(allocator: *std.mem.Allocator, folder: SpecialFolder) Error!?[]const u8;
```