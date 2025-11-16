
## <center>  Bloc & cubit <center/>

**Why Bloc?**
> - separate presentation (ui) from business logic 
> - code fast, easy to test, and reusable.


> #### 0) BlocObserver 
        > - have access to all Changes in one place.
        > - observes all state changes

lib/counter_observer.dart


```python
import 'package:bloc/bloc.dart';

// change name to cubit name
class CounterObserver extends BlocObserver {
 
  const CounterObserver();

  @override
  void onChange(BlocBase<dynamic> bloc, Change<dynamic> change) {
    super.onChange(bloc, change);
    
    print('${bloc.runtimeType} $change');
  }
}
  ================================
  import 'dart:developer';

import 'package:bloc/bloc.dart';
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

class CustomBlocObserver extends BlocObserver {
  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    if (kDebugMode) {
      log('${bloc.runtimeType} $transition');
    }
  }

  @override
  void onChange(BlocBase bloc, Change change) {
    super.onChange(bloc, change);
    if (kDebugMode) {
      log('${bloc.runtimeType} $change');
    }
  }
```

in main.dart


```python
void main() {
  Bloc.observer = const CounterObserver();
  runApp(const CounterApp());
}
```


```python

```

### 1) create states 


```python
part of 'products_cubit.dart';

@immutable
sealed class ProductsState {}

final class ProductsInitial extends ProductsState {}

final class ProductsLoading extends ProductsState {}

final class ProductsLoaded extends ProductsState {
  // can send the model here 
  //    مش متشعبui لو ال
  final List<Product> products;
  ProductsLoaded(this.products);
}

final class ProductsError extends ProductsState {
  final String errorMessage;
  ProductsError(this.errorMessage);
}

```

### 2) create cubit

غير متشعب ui  لو ال 


```python
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
  /* or you can use this method to get all products
  void emitAllProducts() {
    emit(ProductsLoading());
    repository.getAllProduct().then((products) {
      emit(ProductsLoaded(products));
    }).catchError((error) {
      emit(ProductsError(error.toString()));
    });
  }*/
}
====
// .catchError((obj) {
//   // non-200 error goes here.
//   switch (obj.runtimeType) {
//     case DioException:
//       // Here's the sample to get the failed response error code and message
//       final res = (obj as DioException).response;
//       logger.e('Got error : ${res.statusCode} -> ${res.statusMessage}');
//       break;
//   default:
//     break;
//   }
// });
====

```

 >- to access the data in product :                                             
>in BlocBuilder ==>
> send [state.products] which in (ProductsLoaded state) to the widget that contain the ui 

متشعب ui  لو ال


```python
part 'products_state.dart';

class ProductsCubit extends Cubit<ProductsState> {
  ProductsCubit(this.repository) : super(ProductsInitial());
  final Repository repository;
 /*==>*/ late List<Product> products;
  getAllProduct() async {
    emit(ProductsLoading());
    try {
       products = await repository.getAllProduct();
      emit(ProductsLoaded(products)); 
      
    } catch (e) {
      emit(ProductsError(e.toString()));
    }
  }
}

```

to access the data in product => 
BlocProvider.of<ProductsCubit>(context).products.cityName

### 3) provide cubit 

>use BlocProvider in the place that contain 
>* where the state changes
>* where the logic done



BlocProvider provides a bloc to its children ,single instance of a bloc can be provided to multiple widgets within a subtree.BlocProvider will create the bloc lazily, meaning create will get executed when the bloc is looked up via BlocProvider.of<BlocA>(context)
Purpose: Dependency‑injects a Bloc/Cubit instance into its subtree.Lifecycle: Automatically closes the Bloc if it created it

example for matiralApp


```python
BlocProvider(
      create: (context) => ProductsCubit(),
      child: MaterialApp(),
    );
```

### 4) integrate cubit

in the place that change states 


```python
BlocBuilder<ProductsCubit, ProductsState>(
        builder: (context, state) {
          if (state is ProductsLoading) {
            return const Center(child: CircularProgressIndicator());
          } else if (state is ProductsError) {
            return Center(child: Text(state.errorMessage));
          } else if (state is ProductsLoaded) {
            return _buildGridView(state.products);
          } else {
            return const Center(child: Text('No products available'));
          }
        },
      )
```

BlocBuilder
Purpose: Rebuilds its builder callback whenever the bloc’s state changes


### 5) trigger  cubit

> (1) cubit trigger automatically  on app launch 
        >- invoke function in BlocProvider [..getAllProduct()]


```python
BlocProvider(
        create:
            (context) =>
                ProductsCubit(Repository(WebService(Dio())))..getAllProduct(),
        child: const HomePage(),
      )
```

> (2) cubit triggered by functions
          >- functions which can be invoked to trigger state changes in the place that makes the change like {button}


```python
onPressed: () {
        BlocProvider.of<ProductsCubit>(context).getAllProduct();
        // or context.read<ProductsCubit>().getAllProduct();  
        },
```
