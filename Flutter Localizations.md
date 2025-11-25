

# Flutter Localizations (i18n & l10n)   
> Internationalization (i18n) is the process of designing your app so it can adapt to various languages and regions without engineering changes.   

- i18n ==> has 18 letters between the ‘i’ and ‘n’.   
 ---
   
> Localization (l10n):is the actual translation and formatting for a specific locale.   

 This is the process of actually adapting your internationalized app for a specific region or language. This involves providing the translated text and local formats.   
- l10n ==> has 10 letters between the ‘l’ and ‘n’.   
   
   
 ---
## 1. Setup   
### 1.1 Add Dependencies   
```bash
flutter pub add flutter_localizations --sdk=flutter
flutter pub add intl
```  
### 1.2 Enable Code Generation   
In your `pubspec.yaml`, under the `flutter:` section, add:   
```yaml
flutter:
  uses-material-design: true
  # Enables the gen-l10n tool on each build
  generate: true
```  
> Tip: If you prefer manual control, you can always run flutter gen-l10n from the terminal.   

 ---
## 2. Configure Localization Tooling   
Create an `l10n.yaml` file at the project root:   
```yaml
# The name of the file that will contain your default strings (usually English).
arb-dir: lib/l10n  # Directory for .arb files

# The file that will be used as the template for all other translations.
template-arb-file: app_en.arb # Your source (usually English)

# The name of the Dart file that will be generated.
output-localization-file: app_localizations.dart
```  
> Note: Filenames and directory paths are case-sensitive on some platforms.   

 ---
## 3. Create Translation Files (ARB)   
### 3.1 Template File (app_en.arb)   
`lib/l10n/app_en.arb`:   
```json
{
  "@@locale": "en",

  "pageTitle": "Flutter Demo Home Page",
  "@pageTitle": {
    "description": "Main page title"
  },

  "counterText": "You have pushed the button this many times:",

  "helloUser": "Hello {userName}",
  "@helloUser": {
    "description": "Greeting with user’s name",
    "placeholders": {
      "userName": { "type": "String", "example": "Alice" }
    }
  }
}
```  
>    
“@@locale”: “en”: Declares the locale for this file.   
   
“key”: “Value”: A simple key-value pair for a string.   
   
“helloUser”: “Hello {userName}”: A string with a placeholder. The generator will create a method that requires a userName argument.   
   
“@key”: { … }: Optional metadata for developers or translators.   
### 3.2 Additional Locale (app_es.arb)   
`lib/l10n/app_es.arb`:   
```json
{
  "@@locale": "es",

  "pageTitle": "Página de inicio de demostración de Flutter",
  "counterText": "Has presionado el botón tantas veces:",
  "helloUser": "Hola {userName}"
}
```  
> Hint: Keep the same keys across all .arb files. Missing keys will be flagged at build time.   

 ---
## 4. Generate Dart Code   
Simply by saving your .arb files (or by running your app), Flutter’s build tool will see the generate: true flag and the l10n.yaml file and automatically run the code generator.   
   
Flutter’s build runner will automatically generate:   
- `AppLocalizations` class   
- Localization delegates   
- Supported locales list   
   
Or manually:   
```bash
flutter gen-l10n
```  
 ---
## 5. Integrate into MaterialApp   
```dart
import 'package:flutter_gen/gen_l10n/app_localizations.dart';

MaterialApp(
  title: 'Localization Demo',

  // 1. Provide delegates for your strings and Flutter’s built-ins
  localizationsDelegates: AppLocalizations.localizationsDelegates,

  // 2. Declare supported locales
  supportedLocales: AppLocalizations.supportedLocales,

  // 3. Fallback logic if device locale isn’t supported
  localeResolutionCallback: (deviceLocale, supportedLocales) {
    if (deviceLocale != null) {
      for (final locale in supportedLocales) {
        if (locale.languageCode == deviceLocale.languageCode &&
            locale.countryCode == deviceLocale.countryCode) {
          return deviceLocale;
        }
      }
    }
    return const Locale('en'); // default
  },

  // 4. Localize the app title in task switcher
  onGenerateTitle: (context) => AppLocalizations.of(context)!.pageTitle,

  home: const MyHomePage(),
);
```  
 ---
> AppLocalizations.localizationsDelegates: This is a shorthand list provided by the generated code.   
> It includes our AppLocalizations.delegate and the global delegates for Material and Widgets (which translate things like “OK”, “Cancel”, “Search”, etc.).  
> AppLocalizations.supportedLocales: This is a list of Locale objects, also generated automatically from your .arb files.   

##    
## 6. Use Localized Strings in Widgets   
```dart
final l10n = AppLocalizations.of(context)!;

Scaffold(
  appBar: AppBar(
    title: Text(l10n.pageTitle),
  ),
  body: Center(
    child: Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text(l10n.helloUser('Alice')),
        const SizedBox(height: 16),
        Text(l10n.counterText),
        Text('$_counter', style: Theme.of(context).textTheme.headlineMedium),
      ],
    ),
  ),
);
```  
 ---
## 7. Handling Plurals   
Use ICU message syntax in your `.arb` file:   
```json
{
  "clickCounter": "{count, plural, =0{You haven't clicked yet.} =1{You clicked once.} other{You clicked {count} times.}}",
  "@clickCounter": {
    "description": "Click count message",
    "placeholders": { "count": { "type": "int" } }
  }
}
```  
```dart
Text(l10n.clickCounter(count: _counter));
```
> Plural categories: =0, =1, zero, one, two, few, many, other (varies by locale).   

 ---
## 8. Formatting Numbers, Currencies, Dates & Times   
```dart
import 'package:intl/intl.dart';

// Number/Currency
final price = 19.99;
final formattedPrice = NumberFormat.currency(
    locale: AppLocalizations.of(context)!.localeName,
    symbol: '€',
).format(price);

Text('Price: $formattedPrice');

//-------------------------------------------------/

final now = DateTime.now();
final fullDate = DateFormat.yMMMMd(AppLocalizations.of(context)!.localeName).format(now);
final spanishFullDate = DateFormat.yMMMMd('es').format(now);

Text('Today is: $fullDate');
```
 ---
## 9. Tips & Common Pitfalls   
- **Missing keys**: Build will fail if any `.arb` file is missing a key.   
- **Case sensitivity**: File/dir names matter on macOS/Linux.   
- **Re-run**: After changing `.arb`, either save or run `flutter gen-l10n`.   
- **Metadata**: Use `@key` to add translator notes or examples.   
   
Happy localizing! 🎉
