



# <center>Api Flow <center/>

# ( 1 ) create   model   class  from ( json response data )                          using   [json_serializable]

> json_serializable used to =>
Automatically generate code for converting to and from JSON by annotating Dart classes

>add to dev_dependencies:
>
>- build_runner , json_serializable

>add to dependencies:
>
>- json_annotation

**[json_serializable](https://pub.dev/packages/json_serializable)**

```dart
import 'package:json_annotation/json_annotation.dart';

part 'example.g.dart'; //replace "example" with same file name
@JsonSerializable()

class Person {
  // The generated code assumes these values exist in JSON.
  final String firstName;
  final String lastName;

  // The generated code below handles if the corresponding JSON value doesn't exist or is empty.
  // which handles null values 

  final DateTime? dateOfBirth;

  Person({required this.firstName, required this.lastName, this.dateOfBirth});
========================================================
  // Connect the generated [_$PersonFromJson] function to the `fromJson` factory.
                            
  factory Person.fromJson(Map<String, dynamic> json) => _$PersonFromJson(json);
 //                               (json object)     return       (dart object)
=======================================================
  // Connect the generated [_$PersonToJson] function to the `toJson` method.
                             
  Map<String, dynamic> toJson() => _$PersonToJson(this);
  =====================================================
}
```

### if json  responce has spell mistake add @JsonKey(name: "the wrong spelled ") above the attrepute
>
> example  :
> "namwe" is the wrong spelled  in json responce  
> Naming Conventions ==> in json (last_name) => in dart (lastName)

```dart
 class Person {
  @JsonKey(name: "firstnamwe") 
  final String firstName;
  @JsonKey(name: "last_name")
  final String lastName;
 }
```

```dart
dart run build_runner build
```

***(Custom types/non-primitive types) in  json responce***

```dart
import 'package:json_annotation/json_annotation.dart';

part 'product.g.dart';

@JsonSerializable()
class Product {
  @JsonKey(name: 'ID')
  final int id;
  final String title;
  final Dimensions dimensions;

  Product({
    required this.id,
    required this.title,
    required this.dimensions,
  });

  factory Product.fromJson(Map<String, dynamic> json) =>
      _$ProductFromJson(json);
  Map<String, dynamic> toJson() => _$ProductToJson(this);
}

@JsonSerializable()
class Dimensions {
  final double width;
  final double height;
  final double depth;

  Dimensions({
    required this.width,
    required this.height,
    required this.depth,
  });

  factory Dimensions.fromJson(Map<String, dynamic> json) =>
      _$DimensionsFromJson(json);
  Map<String, dynamic> toJson() => _$DimensionsToJson(this);
}

```

>**can use ( json to dart model ) vscode extention to automaticly create the model**
>
>- copy the json response
>- Shift + Ctrl + Alt + G
> then
>- dart run build_runner build

### =============================================================================== ###

## (2) create ( webservices ) class  using

## [Retrofit](https://pub.dev/packages/retrofit) ##

>**pubspec add**
>
>- dependencies:
  retrofit,json_annotation
>- dev_dependencies:
  retrofit_generator,build_runner,json_serializable

```dart
import 'package:dio/dio.dart';
import 'package:retrofit/retrofit.dart';

part 'web_services.g.dart'; 
todo //replace "example" with same file name

@RestApi(baseUrl: '//baseUrl')
abstract class WebServices {
  factory WebServices(Dio dio, {String? baseUrl}) = _WebServices;

  @GET('endpoint')
  Future<response type> getAllUsers();

   @GET('endpoint{id name in json in api}')
  Future<response type> getCharacterById(@Path('//id name in url in api') int pathId);
 //id naming in dart <== pathId
 // @Path()=> api عشان لو تسميه دارت مختلفه عن ال / path_id(api) تعني  pathId(dart) عشان يعرف ان 
 //  pathId in dart , path_id in api json
 // example: 
 @GET('character/{path_id}')
  Future<response type> getCharacterById(@Path('path_id') int pathId);

  @POST('endpoint')
  Future<response type> createUser(@Body() User newUser, @Header('Authorization') String bearerToken); 
  // Headerفي الBearer token  لو طالب انك ترسل 

  @DELETE('endpoint/{id}')
  Future<response type> deleteUser(@Path('id') int id, @Header('Authorization') String bearerToken);
  // if response return nothing you can use dynamic or httpResponse(retrofit docs)
}
```

```dart
dart run build_runner build
```

#### ==================================================================

## (3) create ( Repo ) class

```dart
import 'package:retrofit/dio.dart';

class MyRepo {

// taking instance from WebServices class
  final WebServices webServices;

  MyRepo(this.webServices);
//==========================================

// copy every function from web_services class

 Future<List<UserModel>> getAllUsers() async {
 var responce = await webServices.getAllUsers();
 // retrofit handles the Response {json} and convert it to dart object automaticly
   return responce;
}
//==================
  Future<User> getUserById(String userId) async {
    return await webServices.getUserById(userId);
  }
//==================
Future<User>createNewUser(User newUser) async {
 return await   webServices.createNewUser(newUser,'Bearer 266011b7625eba47bb22d916cc895be80d09523c732855d150f2852347bda0ad');
  }
//====================
Future<dynamic>deleteUser(String userId) async {
 return await   webServices.deleteUser( userId,'Bearer 266011b7625eba47bb22d916cc895be80d09523c732855d150f2852347bda0ad');
  }
//===================
// how to secure the token in the repo class?//
}

```

## (4) create cubit

## (5) create Dependency Injection class    using Get_it

injection.dart

```dart
final getIt = GetIt.instance;
// TODO :  invoke this function in main
void initGetIt() {

// TODO : add bussness layer component ( Cubit,Repo,WebServices)
// used LazySingleton to create instance of class only when it is needed
  getIt.registerLazySingleton<MyCubit>(() => MyCubit(getIt()));
  getIt.registerLazySingleton<MyRepo>(() => MyRepo(getIt()));
  getIt.registerLazySingleton<WebServices>(
      () => WebServices(createAndSetupDio()));
}

  Dio createAndSetupDio() {
    Dio dio = Dio();

    dio
      ..options.connectTimeout = 20 * 1000
      ..options.receiveTimeout = 20 * 1000;
  
  

    dio.interceptors.add(LogInterceptor(
      responseBody: true,
      error: true,
      requestHeader: false,
      responseHeader: false,
      request: true,
      requestBody: true,
      requestHeader: true,
    ));

    return dio;
  }
// SEARCH how to add @Header('Authorization') in dio instance
//https://pub.dev/packages/retrofit#relative-api-baseurl
```

```dart
final getIt = GetIt.instance;

void initGetIt() {
  _registerNetwork();
  _registerRepositories();
  _registerCubits();
}

void _registerNetwork() {
  getIt.registerLazySingleton<Dio>(() => _createAndSetupDio());
  getIt.registerLazySingleton<WebServices>(
    () => WebServices(getIt<Dio>()),
  );
}

void _registerRepositories() {
  getIt.registerLazySingleton<MyRepo>(
    () => MyRepo(getIt<WebServices>()),
  );
}

void _registerCubits() {
  // Factory: new instance every time (e.g. per screen)
  getIt.registerFactory<MyCubit>(
    () => MyCubit(getIt<MyRepo>()),
  );
}

Dio _createAndSetupDio() {
  final dio = Dio()
    ..options
      ..connectTimeout = 20_000
      ..receiveTimeout = 20_000
    ..interceptors.add(
      LogInterceptor(
        request: true,
        requestHeader: true,
        requestBody: true,
        responseBody: true,
        error: true,
      ),
    );
  return dio;
}

```

in BlocProvider {
    create : (ctx) => getIt<'cubitname'>()
}

===============================================================================================================

### <center>**Error Handling**<center/>

add freezed ,freezed_annotation
>
>- model class with less code
>- Code generation for immutable classes that has a simple syntax/API without compromising on the features

in repo file change every method return type to be generic class (succses , failure)

error_model.dart

```dart

import 'package:freezed_annotation/freezed_annotation.dart';



part 'error_model.g.dart';
//change fields to match the json error response  
@JsonSerializable()
class ErrorModel {
  String? field;
  String? message;

  ErrorModel(this.field, this.message);

    factory ErrorModel.fromJson(Map<String, dynamic> json) => _$ErrorModelFromJson(json);

  Map<String, dynamic> toJson() => _$ErrorModelToJson(this);
}

```

network_exceptions.dart

```dart
import 'dart:io';

import 'package:dio/dio.dart';
import 'package:freezed_annotation/freezed_annotation.dart';


part 'network_exceptions.freezed.dart';

@freezed
abstract class NetworkExceptions with _$NetworkExceptions {
  const factory NetworkExceptions.requestCancelled() = RequestCancelled;

  const factory NetworkExceptions.unauthorizedRequest(String reason) =
      UnauthorizedRequest;

  const factory NetworkExceptions.badRequest() = BadRequest;

  const factory NetworkExceptions.notFound(String reason) = NotFound;

  const factory NetworkExceptions.methodNotAllowed() = MethodNotAllowed;

  const factory NetworkExceptions.notAcceptable() = NotAcceptable;

  const factory NetworkExceptions.requestTimeout() = RequestTimeout;

  const factory NetworkExceptions.sendTimeout() = SendTimeout;

  const factory NetworkExceptions.unprocessableEntity(String reason) =
      UnprocessableEntity;

  const factory NetworkExceptions.conflict() = Conflict;

  const factory NetworkExceptions.internalServerError() = InternalServerError;

  const factory NetworkExceptions.notImplemented() = NotImplemented;

  const factory NetworkExceptions.serviceUnavailable() = ServiceUnavailable;

  const factory NetworkExceptions.noInternetConnection() = NoInternetConnection;

  const factory NetworkExceptions.formatException() = FormatException;

  const factory NetworkExceptions.unableToProcess() = UnableToProcess;

  const factory NetworkExceptions.defaultError(String error) = DefaultError;

  const factory NetworkExceptions.unexpectedError() = UnexpectedError;

  static NetworkExceptions handleResponse(Response? response) {
    List<ErrorModel> listOfErrors =
        List.from(response?.data).map((e) => ErrorModel.fromJson(e)).toList();
    String allErrors = listOfErrors
        .map((e) => "${e.field} : ${e.message}  ")
        .toString()
        .replaceAll("(", "")
        .replaceAll(")", "");
    int statusCode = response?.statusCode ?? 0;
    switch (statusCode) {
      case 400:
      case 401:
      case 403:
        return NetworkExceptions.unauthorizedRequest(allErrors);
      case 404:
        return NetworkExceptions.notFound(allErrors);
      case 409:
        return const NetworkExceptions.conflict();
      case 408:
        return const NetworkExceptions.requestTimeout();
      case 422:
        return NetworkExceptions.unprocessableEntity(allErrors);
      case 500:
        return const NetworkExceptions.internalServerError();
      case 503:
        return const NetworkExceptions.serviceUnavailable();
      default:
        var responseCode = statusCode;
        return NetworkExceptions.defaultError(
          "Received invalid status code: $responseCode",
        );
    }
  }

  static NetworkExceptions getDioException(error) {
    if (error is Exception) {
      try {
        NetworkExceptions networkExceptions;
        if (error is DioError) {
          switch (error.type) {
            case DioErrorType.cancel:
              networkExceptions = const NetworkExceptions.requestCancelled();
              break;
            case DioErrorType.connectTimeout:
              networkExceptions = const NetworkExceptions.requestTimeout();
              break;
            case DioErrorType.other:
              networkExceptions =
                  const NetworkExceptions.noInternetConnection();
              break;
            case DioErrorType.receiveTimeout:
              networkExceptions = const NetworkExceptions.sendTimeout();
              break;
            case DioErrorType.response:
              networkExceptions =
                  NetworkExceptions.handleResponse(error.response);
              break;
            case DioErrorType.sendTimeout:
              networkExceptions = const NetworkExceptions.sendTimeout();
              break;
          }
        } else if (error is SocketException) {
          networkExceptions = const NetworkExceptions.noInternetConnection();
        } else {
          networkExceptions = const NetworkExceptions.unexpectedError();
        }
        return networkExceptions;
      } on FormatException {
        return const NetworkExceptions.formatException();
      } catch (_) {
        return const NetworkExceptions.unexpectedError();
      }
    } else {
      if (error.toString().contains("is not a subtype of")) {
        return const NetworkExceptions.unableToProcess();
      } else {
        return const NetworkExceptions.unexpectedError();
      }
    }
  }

  static String getErrorMessage(NetworkExceptions networkExceptions) {
    var errorMessage = "";
    networkExceptions.when(notImplemented: () {
      errorMessage = "Not Implemented";
    }, requestCancelled: () {
      errorMessage = "Request Cancelled";
    }, internalServerError: () {
      errorMessage = "Internal Server Error";
    }, notFound: (String reason) {
      errorMessage = reason;
    }, serviceUnavailable: () {
      errorMessage = "Service unavailable";
    }, methodNotAllowed: () {
      errorMessage = "Method Allowed";
    }, badRequest: () {
      errorMessage = "Bad request";
    }, unauthorizedRequest: (String error) {
      errorMessage = error;
    }, unprocessableEntity: (String error) {
      errorMessage = error;
    }, unexpectedError: () {
      errorMessage = "Unexpected error occurred";
    }, requestTimeout: () {
      errorMessage = "Connection request timeout";
    }, noInternetConnection: () {
      errorMessage = "No internet connection";
    }, conflict: () {
      errorMessage = "Error due to a conflict";
    }, sendTimeout: () {
      errorMessage = "Send timeout in connection with API server";
    }, unableToProcess: () {
      errorMessage = "Unable to process the data";
    }, defaultError: (String error) {
      errorMessage = error;
    }, formatException: () {
      errorMessage = "Unexpected error occurred";
    }, notAcceptable: () {
      errorMessage = "Not acceptable";
    });
    return errorMessage;
  }
}

```

api_result.dart

```dart

import 'package:freezed_annotation/freezed_annotation.dart';
import 'package:youtube_apis/network_exceptions.dart';

part 'api_result.freezed.dart';

@freezed
abstract class ApiResult<T> with _$ApiResult<T> {
  const factory ApiResult.success(T data) = Success<T>;

  const factory ApiResult.failure(NetworkExceptions networkExceptions) =
      Failure<T>;
}
```

repo

```dart
import 'package:retrofit/dio.dart';
import 'package:youtube_apis/api_result.dart';
import 'package:youtube_apis/network_exceptions.dart';
import 'package:youtube_apis/user.dart';
import 'package:youtube_apis/web_services.dart';

class MyRepo {
  final WebServices webServices;

  MyRepo(this.webServices);

  Future<ApiResult<List<User>>> getAllUsers() async {
    try {
      var response = await webServices.getAllUsers();
      return ApiResult.success(response);
    } catch (error) {
      return ApiResult.failure(NetworkExceptions.getDioException(error));
    }
  }

  Future<User> getUserById(String userId) async {
    return await webServices.getUserById(userId);
  }

  Future<ApiResult<User>> createNewUser(User newUser) async {
    try {
      var response = await webServices.createNewUser(newUser,
          'Bearer 266011b7625eba47bb22d916cc895be80d09523c732855d150f2852347bda0ad');

      return ApiResult.success(response);
      
    } catch (error) {
      return ApiResult.failure(NetworkExceptions.getDioException(error));
    }
  }

  Future<HttpResponse> deleteUser(String id) async {
    return await webServices.deleteUser(id,
        'Bearer 266011b7625eba47bb22d916cc895be80d09523c732855d150f2852347bda0ad');
  }
}

```

cubir states ==> result_state.dart

```dart
import 'package:freezed_annotation/freezed_annotation.dart';
import 'package:youtube_apis/network_exceptions.dart';

part 'result_state.freezed.dart';

@freezed
class ResultState<T> with _$ResultState<T> {
  const factory ResultState.idle() = Idle<T>;

  const factory ResultState.loading() = Loading<T>;

  const factory ResultState.success(T data) = Success<T>;

  const factory ResultState.error(NetworkExceptions networkExceptions) =
      Error<T>;
}

```

cubit.dart

```dart
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:youtube_apis/cubit/result_state.dart';
import 'package:youtube_apis/my_repo.dart';
import 'package:youtube_apis/network_exceptions.dart';
import 'package:youtube_apis/user.dart';

class MyCubit extends Cubit<ResultState<User>> {
  final MyRepo myRepo;
  MyCubit(this.myRepo) : super(const Idle());

  // void emitGetAllUsers() async {
  //   var data = await myRepo.getAllUsers();

  //   data.when(success: (List<User> allUsers) {
  //     emit(ResultState.success(allUsers));
  //   }, failure: (NetworkExceptions networkExceptions) {
  //     emit(ResultState.error(networkExceptions));
  //   });
  // }

  // void emitGetUserDetails(String userId) {
  //   myRepo.getUserById(userId).then((userDetails) {
  //     emit(GetUserDetails(userDetails));
  //   });
  // }

  void emitCreateNewUser(User newUser) async {
    var result = await myRepo.createNewUser(newUser);

    result.when(success: (User userData) {
      emit(ResultState.success(userData));
    }, failure: (NetworkExceptions networkExceptions) {
      emit(ResultState.error(networkExceptions));
    });
  }

  // void emitDeleteUser(String id) {
  //   myRepo.deleteUser(id).then((data) {
  //     emit(DeleteUser(data));
  //   });
  // }
}

```

in ui

```dart
  BlocBuilder<MyCubit, ResultState<User>>(
            builder: (context, ResultState<User> state) {
              return state.when(
                idle: () {
                  return const Center(child: CircularProgressIndicator());
                },
                loading: () {
                  return const Center(child: CircularProgressIndicator());
                },
                success: (User userData) {
                  return Container(
                    height: 50,
                    color: Colors.red,
                    child: Center(
                        child: Text(
                      userData.email.toString(),
                      style: const TextStyle(color: Colors.white),
                    )),
                  );
                },
                error: (NetworkExceptions error) {
                  return Center(
                      child: Text(NetworkExceptions.getErrorMessage(error)));
                },
              );
            },
          ),
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNzYxNzA4ODNdfQ==
-->