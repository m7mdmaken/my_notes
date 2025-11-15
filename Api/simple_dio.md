
## <center>api request with dio & cubit </center>
  


```python
class Product {
  final String title;
  final String thumbnail;
  final double price;

  Product({required this.title, required this.thumbnail, required this.price});

  factory Product.fromJson(Map<String, dynamic> json) {
    return Product(
      title: json['title'] as String,
      thumbnail: json['thumbnail'] as String,
      price: (json['price'] as num).toDouble(),
    );
  }
}

```

__2)  create WebService Class__

>it takes the API url and return the response.data (json map)


```python
import 'package:dio/dio.dart';
//change function name to your own function name
// change the url to your own API endpoint
  
class WebService {
  final String baseUrl = 'https://your-api-endpoint.com';
  final String apiKey = 'your_api_key_here';
  final Dio dio;
  WebService(this.dio);
  
  
  Future<Map<String, dynamic>> fetchProducts() async {
    final response = await dio.get('$baseUrl/products');
    return response.data;
  }

  Future<Map<String, dynamic>> fetchProductById(String id) async {
    final response = await dio.get('$baseUrl/products/$id');
    return response.data;
  }
}

```

3) create repo

> * abstraction layer between the data sources (like API, local database) and the business logic (_Separation of Concerns_)
> * takes the methods in the wevservices class and maps them to dart object using fromJson
> * Error Handling [19-weatherapp]

“Separation of Concerns” (SoC) is a software‐engineering principle that says you should divide a system into distinct sections, each responsible for a specific aspect or “concern” of the application. By keeping these concerns separate, each module can be developed, tested, and maintained independently, improving clarity and reducing the risk that changes in one part will have unintended effects in another.

Key Points
Single Responsibility
Each component or module should have one reason to change. If a module mixes multiple responsibilities—say, fetching data and rendering UI—then any change to how data is fetched could force you to alter UI code, and vice versa.

Layers of an Application

Presentation/UI Layer
Widgets, views, and user‐interaction logic.

Business/Domain Layer
Core application logic, rules, and workflows.

Data/Infrastructure Layer
API calls, database access, caching.

Benefits

Maintainability: Isolate and update one concern without fear of breaking others.

Testability: Write targeted unit tests for each layer.

Reusability: Swap or reuse modules (e.g., replace one data source with another).

Parallel Development: Teams can work on different concerns simultaneously.


```python
import 'package:dio_cubit_saber/product_model.dart';
import 'package:dio_cubit_saber/services/web_service.dart';

class Repository {
  final WebService webService;
  Repository(this.webService);
  Future<List<Product>> getAllProduct() async {
    try {
      final jsonData = await webService.getProductResponse();
    List<dynamic> products = jsonData['products'];
    return (products).map((product) => Product.fromJson(product)).toList(); 
    } on DioExeption catch (e) {
      final errorMessage = e.response?.data['error']['message'] ?? 'Unknown error';
      throw Exception(errorMessage);
      } catch (e) {
        log(e.toString());
        throw Exception('Failed to load products: $e');
      }
    }
    
  }
}

```

4) states


```python
part of 'products_cubit.dart';

@immutable
sealed class ProductsState {}

final class ProductsInitial extends ProductsState {}

final class ProductsLoading extends ProductsState {}

final class ProductsLoaded extends ProductsState {
  final List<Product> products;
  ProductsLoaded(this.products);
}

final class ProductsError extends ProductsState {
  final String errorMessage;
  ProductsError(this.errorMessage);
}

```

5) cubit 


```python
import 'package:dio_cubit_saber/services/repo.dart';
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:dio_cubit_saber/product_model.dart';

part 'products_state.dart';

class ProductsCubit extends Cubit<ProductsState> {
  ProductsCubit(this.repository) : super(ProductsInitial());
  final Repository repository;
  getAllProduct() async {
    emit(ProductsLoading());
    try {
      final List<Product> products = await repository.getAllProduct();
      emit(ProductsLoaded(products));
    } catch (e) {
      emit(ProductsError(e.toString()));
    }
  }
}

```
