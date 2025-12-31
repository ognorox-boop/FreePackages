# ArchiSteamFarm and FreePackages Copilot Instructions

## Architecture Overview

This codebase consists of two main C# projects using .NET 10.0:

- **ArchiSteamFarm (ASF)**: Core application for Steam card farming and automation. Entry point in `ArchiSteamFarm/ArchiSteamFarm/Program.cs`. Main components:
  - `Core/ASF.cs`: Static class managing global state, bots, and services
  - `Steam/Bot.cs`: Represents individual Steam accounts with farming logic
  - `Plugins/`: Extensible plugin system via MEF (System.Composition)
  - `IPC/`: Web API controllers for remote management
  - `Web/`: HTTP client with rate limiting and Steam-specific handling

- **FreePackages**: Plugin that monitors Steam PICS changes and Reddit for free packages, activating them automatically. Implements multiple plugin interfaces (`IASF`, `ISteamPICSChanges`, `IBotCommand2`, etc.).

Data flows from SteamKit2 callbacks → plugin event handlers → bot actions → Steam API calls.

## Build and Run

- **Build**: Use `dotnet publish` with Release config and `net10.0` target framework. See `build.bat`/`build.sh` for full pipeline including submodule updates and documentation generation.
- **Run**: Execute published DLL. ASF loads plugins from `plugins/` subfolder. Config files in `config/` directory (JSON format).
- **Debug**: Run in IDE with debugger attached. For plugin development, build and copy DLL to ASF's `plugins/` folder.

## Testing

- **Framework**: MSTest for both ASF and FreePackages test projects
- **Run**: `dotnet test` on test project or solution
- **Structure**: Tests in `*.Tests/` folders with `TestData/` for fixtures
- **Coverage**: Focus on unit tests for utilities; integration tests for Steam interactions

## Key Conventions

- **Localization**: All user-facing strings in `Localization/Strings.resx` files with embedded resource generation
- **Logging**: Use `ArchiLogger` instances (e.g., `ASF.ArchiLogger.LogGenericInfo()`)
- **Configuration**: JSON-based with `JsonElement` parsing. Bot-specific configs override global
- **Async/Await**: All I/O operations async with `ConfigureAwait(false)`
- **Error Handling**: `ArgumentNullException.ThrowIfNull()` for validation; exceptions logged via ArchiLogger
- **Collections**: Prefer `FrozenSet/Dictionary` for immutable data; `ConcurrentDictionary` for shared state
- **SteamKit2 Integration**: Wrap Steam client calls in try-catch; handle rate limits via semaphores

## Plugin Development

- Implement `IPlugin` and specific interfaces (e.g., `IBotCommand2` for commands)
- Export via `[Export(typeof(IPlugin))]` attribute
- Access bots via `Bot.Bots` dictionary
- Commands prefixed with plugin name (e.g., `!qsa` for FreePackages status)
- Config per-bot in `BotName.json` with plugin-specific properties

## External Dependencies

- **SteamKit2**: Steam protocol handling
- **NLog**: Structured logging with custom targets
- **AngleSharp**: HTML parsing for web scraping
- **System.Composition**: Plugin loading
- **Reddit API**: Via ASFinfo for free game monitoring

## Common Patterns

- **Rate Limiting**: Use `ASF.WebLimitingSemaphores` for HTTP requests
- **Caching**: Implement `GlobalCache` for persistent data with JSON serialization
- **Filters**: Configurable via `FilterConfig` classes with JSON deserialization
- **Background Tasks**: Use `Task.Delay()` with cancellation tokens for polling
- **Versioning**: Semantic versioning with `Assembly.GetName().Version`

## File Structure Examples

- `ArchiSteamFarm/ArchiSteamFarm/Steam/Cards/Game.cs`: Card farming logic per game
- `FreePackages/FreePackages/Handlers/PackageHandler.cs`: Package activation workflow
- `ArchiSteamFarm/ArchiSteamFarm/IPC/Controllers/Api/BotController.cs`: REST API for bot management</content>
<parameter name="filePath">c:\Users\ognor\Downloads\FreePackages\.github\copilot-instructions.md
