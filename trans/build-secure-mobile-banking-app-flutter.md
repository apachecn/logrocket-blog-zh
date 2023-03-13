# 用 Flutter 构建安全的手机银行应用

> 原文：<https://blog.logrocket.com/build-secure-mobile-banking-app-flutter/>

银行应用最重要的一个方面是安全性。每个人都想把他们的财富放在一个安全的地方。在当今世界，金钱几乎驱动一切，我们总是想获得我们的现金。好吧，我们怎么做呢？最常见的方法之一是使用我们随身携带的设备:我们的手机。

因此，如果我们想开发一个管理用户资金的应用程序，我们必须确保它尽可能的安全。在本文中，我们将了解使用 Flutter 构建安全的移动银行应用程序的基本方面，并查看如何使用 Flutter 框架实现它们的示例代码。

*向前跳转:*

## 使用 Flutter 进行安全通信

移动银行应用程序经常与后端服务器通信。要考虑的最重要的事情之一是为这种通信使用安全层。幸运的是，`HTTPS`伸出了援助之手，阻止其他人嗅探我们从后端系统发送和接收的数据。

`HTTPS`的工作方式与简单的`HTTP`相同，但是在`TCP`通信层和`HTTPS`协议`TLS`或`SSL`之间插入了一个安全层。

### 使用`HTTPS`配合颤振

安全套接字层(Secure Sockets Layer，`SSL`)是关于如何在网络通信中保护消息的第一个定义。经过几次迭代后，传输层安全性`TSL`被定义并替换为`SSL`。`TLS`定义客户端-服务器应用的安全通信方法。它提供公钥和私钥对来保护客户端和服务器之间的消息。

当客户端连接到服务器时，它将收到自己的公钥。客户端发送到服务器的每条消息都是基于公钥加密的。只有拥有私钥的服务器才能解密消息。如果有人可以嗅探网络通信，他们将无法读取请求的原始内容。

我们如何在应用程序中使用它呢？这很简单。我们将只与提供`HTTPS`入口点的 API 通信。就是这样；我们结束了，对吗？很不幸，不是。

为了确保具有`HTTPS`的端点可以被信任，服务器应该提供由可信证书颁发机构签名的证书。浏览器对此进行检查，并决定端点是否具有有效、可信任的证书。基于此，他们将向用户显示警告。

当构建通过`HTTPS`与服务器通信的 Flutter 应用程序时，我们可以依赖像 [http](https://pub.dev/packages/http) 和 [dio](https://pub.dev/packages/dio) 这样的包。我们唯一需要做的就是在请求中使用一个以`HTTPS`开头的 URL。

带有`dio`的示例应该是这样的:

```
var response = await Dio().get('https://www.google.com');

```

有时，后端系统可以使用自签名证书。当您构建自己的后端并在开发时在您的机器上运行时，就会发生这种情况。自签名证书意味着它是由您签名的，而不是由可信机构签名的。

创建它很容易，也很自由，但不提供安全性。所以，当你想与这样的 API 通信时，你会得到一个异常。您可以通过向您的`HTTP client`分配一个自定义证书回调来绕过证书有效性检查:

```
(dio.httpClientAdapter as DefaultHttpClientAdapter).onHttpClientCreate  = (client) {
  client.badCertificateCallback=(X509Certificate cert, String host, int port){
    if(kDebugMode){ // Accept it only in debug mode.
      return true;
    }
    return false;
  };
};

```

## 使用 Flutter 包安全地存储数据

并非每个应用程序都希望将其数据存储在设备之外。如果你想把所有东西都保存在用户的手机上，并且[仍然安全地存储数据](https://blog.logrocket.com/securing-local-storage-flutter/)，你将需要某种加密的数据存储。有一个存储令牌的地方也很有帮助，它提供了对 API 的访问。以下软件包有助于颤振:

### 实现 flutter_secure_storage 包

[flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 包提供了一个键值存储，你可以在这里保存你的小秘密，这些小秘密将被加密。它与 [shared_preferences](https://blog.logrocket.com/using-sharedpreferences-in-flutter-to-store-data-locally/) 非常相似，只是多了一个安全层。

让我们看一个例子:

![Example of a Secure Mobile Banking Application with Flutter](img/ecaa848587dec412666787d602c877fb.png)

以下小部件显示并修改我们的小秘密:

```
// my_secret.dart
import 'package:flutter/material.dart';

/// This widget displays and modifies your little secret.
class MySecret extends StatelessWidget {
  const MySecret({
    super.key,
    required this.readMySecret,
    required this.changeMySecret,
    required this.deleteMySecret,
  });

  /// A callback that can read the secret.
  final Future<String?> Function() readMySecret;

  /// A callback that can change the value of the secret.
  final Future<void> Function() changeMySecret;

  /// A callback that can delete your secret.
  final Future<void> Function() deleteMySecret;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Column(
        children: [
          Text(
            'My secret',
            style: Theme.of(context).textTheme.headline3,
          ),
          FutureBuilder(
            future: readMySecret(),
            builder: (context, snapshot) {
              if (snapshot.connectionState == ConnectionState.done) {
                return Text(snapshot.data ?? 'Provide your little secret');
              }
              return const CircularProgressIndicator();
            },
          ),
          ElevatedButton(
            onPressed: changeMySecret,
            child: const Text('Change'),
          ),
          ElevatedButton(
            onPressed: deleteMySecret,
            child: const Text('Delete'),
          ),
        ],
      ),
    );
  }
}

```

让我们使用上面的小部件，并用 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 功能对其进行配置:

```
 // secure_storage_demo.dart
import 'package:flutter/material.dart';
import 'package:flutter_secure_storage/flutter_secure_storage.dart';
import 'package:secure_banking_app/change_secret_dialog.dart';
import 'package:secure_banking_app/my_secret.dart';

const _mySecretValueKey = 'mySecretValue';

/// Displays the usage of the flutter_secure_storage example.
class SecureStorageDemo extends StatefulWidget {
  const SecureStorageDemo({super.key});

  @override
  State<SecureStorageDemo> createState() => _SecureStorageDemoState();
}

class _SecureStorageDemoState extends State<SecureStorageDemo> {
  /// Create a new instance of the secure storage
  late final _secureStorage = const FlutterSecureStorage();

  /// Changes the current secret value by showing an edit dialog
  Future<void> _changeMySecret() async {
    final scaffoldMessenger = ScaffoldMessenger.of(context);

    /// Read the actual value
    final currentSecret = await _secureStorage.read(key: _mySecretValueKey);
    // Display a dialog with a text field where the user can change their secret.
    final newSecret = await showDialog(
      context: context,
      builder: (context) => ChangeSecretDialog(
        currentSecret: currentSecret,
      ),
    );
    if (newSecret != null) {
      // Save the secret value in the secure storage.
      await _secureStorage.write(key: _mySecretValueKey, value: newSecret);
      setState(() {});
      scaffoldMessenger.showSnackBar(
        const SnackBar(
          content: Text(
            'Your little secret is updated.',
          ),
        ),
      );
    }
  }

  /// Removes the current value.
  Future<void> _deleteMySecret() async {
    final scaffoldMessenger = ScaffoldMessenger.of(context);
    // Deletes a key from the secure storage.
    await _secureStorage.delete(key: _mySecretValueKey);
    setState(() {});
    scaffoldMessenger.showSnackBar(
      const SnackBar(
        content: Text(
          'Your little secret is removed.',
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return MySecret(
      readMySecret: () => _secureStorage.read(key: _mySecretValueKey),
      changeMySecret: _changeMySecret,
      deleteMySecret: _deleteMySecret,
    );
  }
}

```

使用图书馆非常容易；您只需将其导入并在安全存储中创建一个新实例:

```
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

late final secureStorage = const FlutterSecureStorage();

```

当您想要访问保存在特定键下的安全存储中的值时，您可以调用:

```
final currentSecret = await secureStorage.read(key: _mySecretValueKey);

```

要改变它，你可以写一个新的值。注意，如果值是`null`，它将从存储器中删除密钥:

```
await secureStorage.write(key: _mySecretValueKey, value: newSecret);

```

或者，您可以通过以下方式将您的小秘密从储物空间中移除:

```
await secureStorage.delete(key: _mySecretValueKey);

```

### 使用 Flutter 生物识别存储包

[biometric_storage](https://pub.dev/packages/biometric_storage) 提供了一个非常相似的方法，并增加了一些特性。它可以存储类似于 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 包的小数据，但可以选择用生物锁来保护它。只有当用户[用他们的指纹](https://blog.logrocket.com/implementing-face-recognition-authentication-flutter/)、面部识别号、个人识别码或图案解锁时，他们才能访问存储器中的数据。你需要按照每个平台的[安装步骤](https://github.com/authpass/biometric_storage/#installation)在你的应用中使用这个包。

我们可以重用`MySecret`小部件来显示和修改我们生物特征存储器中的秘密。我们只需要为操作创建回调:

```
// biometric_storage_demo.dart
import 'package:biometric_storage/biometric_storage.dart';
import 'package:flutter/material.dart';
import 'package:secure_banking_app/my_secret.dart';

import 'change_secret_dialog.dart';

const _myStorageName = 'mystorage';

/// Displays the content of the secret storage and provides operations to
/// change its value.
class BiometricStorageDemo extends StatelessWidget {
  const BiometricStorageDemo({super.key});

  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
      // Let's check whether the device supports the biometric storage.
      future: BiometricStorage().canAuthenticate(),
      builder: (context, snapshot) {
        if (snapshot.connectionState == ConnectionState.done) {
          if (snapshot.data != CanAuthenticateResponse.success) {
            return const Center(
              child: Text('Can not use biometric storage'),
            );
          }
          // We have the functionality, show the content.
          return _BiometricStorageProvider(
            builder: (secureStorage) => _BiometricStorageDemoPage(
              secureStorage: secureStorage,
            ),
          );
        }
        return const Center(
          child: CircularProgressIndicator(),
        );
      },
    );
  }
}

/// Provides a secure storage through its builder.
class _BiometricStorageProvider extends StatelessWidget {
  const _BiometricStorageProvider({
    Key? key,
    required this.builder,
  }) : super(key: key);

  /// Bulder function that provides access to the secure storage.
  final Widget Function(BiometricStorageFile) builder;

  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
        future: BiometricStorage().getStorage(_myStorageName),
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.done &&
              snapshot.hasData) {
            return builder(snapshot.data!);
          }
          return const Center(
            child: CircularProgressIndicator(),
          );
        });
  }
}

/// Displays the content of the secure storage and operations to manipulate
/// it.
class _BiometricStorageDemoPage extends StatefulWidget {
  const _BiometricStorageDemoPage({
    super.key,
    required this.secureStorage,
  });

  final BiometricStorageFile secureStorage;

  @override
  State<_BiometricStorageDemoPage> createState() =>
      _BiometricStorageDemoPageState();
}

class _BiometricStorageDemoPageState extends State<_BiometricStorageDemoPage> {
  /// Changes the current secret value by showing an edit dialog
  Future<void> _changeMySecret() async {
    final scaffoldMessenger = ScaffoldMessenger.of(context);

    /// Read the actual value
    final currentSecret = await widget.secureStorage.read();
    // Display a dialog with a text field where the user can change their secret.
    final newSecret = await showDialog(
      context: context,
      builder: (context) => ChangeSecretDialog(
        currentSecret: currentSecret,
      ),
    );
    if (newSecret != null) {
      // Save the secret value in the secure storage.
      await widget.secureStorage.write(newSecret);
      setState(() {});
      scaffoldMessenger.showSnackBar(
        const SnackBar(
          content: Text(
            'Your little secret is updated.',
          ),
        ),
      );
    }
  }

  /// Removes the current value.
  Future<void> _deleteMySecret() async {
    final scaffoldMessenger = ScaffoldMessenger.of(context);
    // Deletes a key from the secure storage.
    await widget.secureStorage.delete();
    setState(() {});
    scaffoldMessenger.showSnackBar(
      const SnackBar(
        content: Text(
          'Your little secret is removed.',
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return MySecret(
      readMySecret: widget.secureStorage.read,
      changeMySecret: _changeMySecret,
      deleteMySecret: _deleteMySecret,
    );
  }
}

```

该包以基于文件的方式处理机密。它可以创建一个由你的生物特征保护的加密文件，你可以把任何字符串放入其中。如果你想存储多个值，你必须创建不同的存储。这与 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 包不同，在后者中，您可以向存储中添加多个具有不同密钥的值。

我们要做的第一件事就是用`BiometricStorage().canAuthenticate(), // Check whether the device supports biometric auth`检查设备是否支持生物认证。

如果设备支持，我们需要使用`BiometricStorage().getStorage(_myStorageName)`创建一个新的存储。这将创建一个由生物特征加密和保护的`myStorageName`文件。您可以将任何字符串保存到此存储中。如果你需要其他类型，你必须提供类型`String`的转换。

您可以使用`await secureStorage.read()`从存储器中读取数据，并使用`write() await widget.secureStorage.write(newSecret);`更改数值。

如果你不想保留你的`secret`，你可以用`await widget.secureStorage.delete();`移除它。

### 将 Hive 集成到您的 Flutter 应用程序中

当我们只想存储少量数据(如令牌或密码)时，前面提到的包非常有用。但有时，我们需要一种方法来存储更多的信息。如果我们要加密所有的数据，我们可以在应用程序中使用 [hive 数据库](https://blog.logrocket.com/handling-local-data-persistence-flutter-hive/)。

Hive 是一个键值数据库，它提供了对数据的快速访问，并且是用 dart 编写的。将它集成到颤振应用程序中是非常容易的。

Hive 与类似于`Map`结构的`boxes`一起工作。一旦你创建了一个盒子，你就可以根据它的键来放置和获取值。一个框可以包含任何类型的值，但您只能将其限制为一种类型:

```
var box = await Hive.openBox('account');
await box.put('balance', 42.0);
double balance = box.get('balance');
await box.close();

```

Hive 还可以处理自定义对象，并提供一个代码生成器来简化您的工作:

```
@HiveType(typeId: 0)
class Account extends HiveObject {

  @HiveField(0)
  double balance;
}

final account = Account();
account.balance = 42.0;
box.add(account); // Hive assigns a key automatically

account.balance = 21.0;
account.save();

```

您可以告诉 Hive 在打开盒子时对其内容进行加密:

```
final encryptedBox= await Hive.openBox(
  'account', 
  encryptionCipher: HiveAesCipher('encryption key'),
);

```

Hive 只会加密数据，不会加密`keys`。您将需要使用相同的`encryption key`,因为 Hive 在打开盒子时不会检查它。您可以使用以下内容生成一个新的:

```
final key = Hive.generateSecureKey();

```

因为即使在应用程序关闭并再次打开后，您也需要拥有相同的`key`,所以您需要安全地存储它。例如，你可以为它使用 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 。

让我们来看一个完整的例子:

```
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:flutter_secure_storage/flutter_secure_storage.dart';
import 'package:hive_flutter/hive_flutter.dart';

const _boxName = 'account';
const _encryptionKey = 'boxEncryptionKey';
const _balanceKey = 'balance';

class HiveDemo extends StatelessWidget {
  const HiveDemo({super.key});

  /// Opens the Hive box
  Future<void> _openBox() async {
    // Initialize Hive for Flutter
    await Hive.initFlutter();
    // Read the key used for encryption.
    const secureStorage = FlutterSecureStorage();
    String? encryptionKey = await secureStorage.read(key: _encryptionKey);
    if (encryptionKey == null) {
      // Create a new key if it does not exists.
      final key = Hive.generateSecureKey();
      // Add it to the secure storage.
      await secureStorage.write(
        key: _encryptionKey,
        value: base64UrlEncode(key),
      );
    }
    // Read the key from the secure storage.
    final key = await secureStorage.read(key: _encryptionKey);
    final encryptionKeyBytes = base64Url.decode(key!);
    // Open the box with the encryption key.
    await Hive.openBox<double>(
      _boxName,
      encryptionCipher: HiveAesCipher(encryptionKeyBytes),
    );
  }

  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
      future: _openBox(),
      builder: (context, snapshot) {
        if (snapshot.connectionState == ConnectionState.done) {
          return const _HiveDemoContent();
        }
        return const Center(
          child: CircularProgressIndicator(),
        );
      },
    );
  }
}

/// Displays the balance and modifies it.
class _HiveDemoContent extends StatelessWidget {
  const _HiveDemoContent({super.key});

  @override
  Widget build(BuildContext context) {
    // ValueListenableBuilder reacts to changes in the given box.
    // It will run its builder method when any value in the box changes.
    return ValueListenableBuilder(
      valueListenable: Hive.box<double>(_boxName).listenable(),
      builder: (context, box, child) => Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'Balance',
              style: Theme.of(context).textTheme.headline3,
            ),
            // Get the current value of the balance from the box.
            Text(
              box.get(_balanceKey, defaultValue: 0.0).toString(),
            ),
            ElevatedButton(
              onPressed: () {
                // Get the balance value from the box.
                final balance = box.get(_balanceKey, defaultValue: 0.0);
                // Update it with the new value.
                box.put(_balanceKey, balance! + 5.0);
              },
              child: const Text('Add \$5'),
            )
          ],
        ),
      ),
    );
  }
}

```

这里的关键部分是你如何打开你的盒子。`_openBox()`方法试图通过使用 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 包来读取我们之前用于加密的密钥。如果密钥不存在，我们创建一个新的并保存在存储器中。一旦我们有了钥匙，我们就打开盒子。如果盒子打开了，我们就可以开始使用它了。`ValueListenableBuilder`可以观察盒子的变化,[可以通过它的`builder`方法更新 UI](https://blog.logrocket.com/improving-flutter-ui-chip-widget/) 。我们放入盒子中的所有数据都将被加密，因此没有人能够读取在文件系统上存储盒子内容的物理文件。

## 通过锁定应用程序来保护您的 Flutter 应用程序

当管理敏感数据时，例如管理用户的钱，不允许任何人进入应用程序是很重要的。以加密的方式存储数据使得不允许从应用程序之外读取数据变得安全。你可以使用设备的生物识别锁来阻止访问应用程序的功能。您可以通过使用 [local_auth](https://pub.dev/packages/local_auth) 包将这样的认证添加到您的 Flutter 应用程序中。在开始使用之前，请遵循软件包中针对特定平台的[安装说明](https://github.com/flutter/plugins/tree/main/packages/local_auth/local_auth#ios-integration):

```
// local_auth_demo.dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:local_auth/local_auth.dart';

/// Displays sensitive information only after the user has authenticated with
/// biometrics.
class LocalAuthDemo extends StatefulWidget {
  const LocalAuthDemo({super.key});

  @override
  State<LocalAuthDemo> createState() => _LocalAuthDemoState();
}

class _LocalAuthDemoState extends State<LocalAuthDemo> {
  final auth = LocalAuthentication();

  /// Check whether the device supports authentication
  Future<bool> _isLocalAuthAvailableOnDevice() async {
    /// Check whether the device is capable of using biometrics
    final isSupported = await auth.isDeviceSupported();
    if (!isSupported) {
      return false;
    }
    try {
      /// Check whether it is configured on the device.
      return auth.canCheckBiometrics;
    } on PlatformException catch (e) {
      return false;
    }
  }

  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
      future: _isLocalAuthAvailableOnDevice(),
      builder: (context, snapshot) {
        if (snapshot.connectionState == ConnectionState.done) {
          if (snapshot.hasData && snapshot.data!) {
            return _LocalAuthenticator(auth: auth);
          }
          return const Center(
            child: Text(
              'Can not authenticate with biometrics. PLease enable it on your device.',
            ),
          );
        }
        return const Center(
          child: CircularProgressIndicator(),
        );
      },
    );
  }
}

/// Does the authentication and displays the sensitive information based on its
/// result
class _LocalAuthenticator extends StatelessWidget {
  const _LocalAuthenticator({
    super.key,
    required this.auth,
  });

  final LocalAuthentication auth;

  /// Authenticate the user with biometrics. The method will be chosen by
  /// the OS.
  Future<bool> _authenticate() => auth.authenticate(
        localizedReason: 'Please auth yourself',
        options: const AuthenticationOptions(
          stickyAuth: true,
        ),
      );

  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
      future: _authenticate(),
      builder: (context, snapshot) {
        if (snapshot.connectionState == ConnectionState.done) {
          if (snapshot.data!) {
            return const Center(
              child: Text('Welcome! You can see your sensitive data.'),
            );
          }
          return const Center(
            child:
                Text('Sorry, but you are not allowed to access to this page.'),
          );
        }
        return const Center(
          child: CircularProgressIndicator(),
        );
      },
    );
  }
}

```

当使用 [local_auth](https://pub.dev/packages/local_auth) 包时，我们必须检查设备是否能够进行生物认证，并且配置是否正确:

```
// Check whether the device is capable of using biometrics
final isSupported = await auth.isDeviceSupported();
// ...
try {
  /// Check whether it is configured on the device.
  return auth.canCheckBiometrics;
} on PlatformException catch (e) {
  return false;
}

```

一旦我们知道可以使用生物识别技术，我们就可以使用`authenticate()`功能来验证用户:

```
await authenticate(
  localizedReason: 'Please auth yourself',
  options: const AuthenticationOptions(
    stickyAuth: true,
  ),
);

```

## 隐藏敏感信息以保护您的 Flutter 应用程序

安全应用程序的另一个重要方面是在应用程序不运行时隐藏敏感数据。通常，当用户在运行的应用程序之间切换时，会有一个应用程序的预览。使用 [secure_application](https://pub.dev/packages/secure_application) 包，您可以向您的应用程序添加一个覆盖图，以便在应用程序处于后台时隐藏其内容。

除了覆盖之外，软件包还会锁定应用程序的不同部分，所以当用户回来时，他们需要解锁它。解锁机制可以由用户使用例如 local_auth 或任何类型认证流来实现。一旦我们授予用户访问权限，我们就可以解锁隐藏的内容。让我们来看看它的实际应用:

```
// secure_application.dart
import 'package:flutter/material.dart';
import 'package:secure_application/secure_application.dart';

class SecureApplicationDemo extends StatelessWidget {
  const SecureApplicationDemo({super.key});

  @override
  Widget build(BuildContext context) {
    // Wrap the application and mark it as a secure one.
    return SecureApplication(
      nativeRemoveDelay: 800,
      // This callback method will be called when the app becomes available again
      // and its content was hidden with an overlay. We can provide logic to
      // unlock the content again. Here we can use any auth logic, for example
      // biometrics with the local_auth package.
      onNeedUnlock: (secureApplicationStateNotifier) {
        print(
          'Needs to be unlocked. You can use any auth method, e.g local_auth to unlock the content',
        );
        return null;
      },
      child: _SecureApplicationContent(),
    );
  }
}

class _SecureApplicationContent extends StatefulWidget {
  const _SecureApplicationContent({super.key});

  @override
  State<_SecureApplicationContent> createState() =>
      _SecureApplicationContentState();
}

class _SecureApplicationContentState extends State<_SecureApplicationContent> {
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    // Enable the feautre. This will add overlay when our app goes background.
    SecureApplicationProvider.of(context)?.secure();
  }

  @override
  Widget build(BuildContext context) {
    // Wrap the sensitive part with SecureGate.
    // This will hide the sensitive part until we unlock the content.
    return SecureGate(
      blurr: 60,
      opacity: 0.8,
      // The content of this builder will be displayed after our app
      // comes back to foreground and its content was hidden with the overlay.
      lockedBuilder: (context, secureApplicationController) => Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text('Content is locked!'),
            ElevatedButton(
              onPressed: () {
                // Unlock the content manually.
                secureApplicationController?.unlock();
              },
              child: Text('Unlock'),
            ),
          ],
        ),
      ),
      child: const Center(
        child: Text(
          'Your sensitive data',
        ),
      ),
    );
  }
}

```

[安全应用](https://pub.dev/packages/secure_application)包提供了两个有价值的小部件。第一个是`SecureApplication`，负责管理叠加层，并提供一个控制器来管理安全状态的状态。另一个是`SecureGate`，当内容被锁定时，它隐藏其子节点。当应用程序从后台返回时，就会发生这种情况。

您首先必须通过调用控制器上的`secure()`方法来启用安全特性:

```
SecureApplicationProvider.of(context)?.secure();

```

您可以在`SecureApplication`的后代小部件中访问控制器。一旦启用，它会自动处理覆盖。

您必须通过控制器解锁受保护的内容，以暴露敏感数据。这可以在`SecureApplication`的`onNeedUnlock`回调中完成:

```
SecureApplication(
      // This callback method will be called when the app becomes available again
      // and its content was hidden with an overlay. We can provide logic to
      // unlock the content again. Here we can use any auth logic, for example
      // biometrics with the local_auth package.
      onNeedUnlock: (secureApplicationStateNotifier) {
        // Use any authentication method.
      },
      child: _SecureApplicationContent(),
    );

```

在这个回调中，您可以实现适合您的应用程序的任何身份验证方法。您可以使用 [local_auth](https://pub.dev/packages/local_auth) 基于生物特征认证用户，或者检查存储的`JWT token`是否仍然有效。

`SecureGate`小部件有两个基本属性:

```
SecureGate(
      // The content of this builder will be displayed after our app
      // comes back to the foreground and its content was hidden with the overlay.
      lockedBuilder: (context, secureApplicationController) {
        // Return the widgets shown when it is locked.
        // Unlock the app through the controller.
      },
      child: ...,
    );

```

`child`属性保存敏感数据。如果内容被锁定，它将被隐藏。在这种情况下，`lockedBuilder`回调返回用户应该看到的内容。您可以通过`secureApplicationController`参数手动解锁内容。

# 摘要

在这篇文章中，我试图收集实现处理敏感数据的 Flutter 应用程序(如移动银行应用程序)时所需的不同安全要求。让访问内容变得困难是很重要的，所以我们需要在与服务器通信时总是使用`HTTPS`。当我们在本地存储数据时，我们应该尝试对其进行加密，并且只有在用户验证自己的身份后才允许访问。

另一个重要方面是当用户在运行的应用程序之间切换时隐藏敏感数据。幸运的是，伟大的 Flutter 社区已经制作了包，我们可以轻松地将它们集成到我们的应用程序中，使其更加安全。

所有的源代码都可以在[这里](https://github.com/dtengeri/secure_app_demo)获得。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)