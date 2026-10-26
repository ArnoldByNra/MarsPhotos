1. El repositorio separa “de dónde vienen los datos” de “cómo se muestran”. Antes de esta práctica, el MarsViewModel llamaba directamente a MarsApi (Retrofit). Con el repositorio (MarsPhotosRepository y su implementación NetworkMarsPhotosRepository), el ViewModel solo sabe que pide fotos y no si vienen de la red, de una base de datos o de un caché. Si mañana cambias la fuente de datos, el ViewModel no se toca.

2. Depender de una interfaz, no de una clase concreta, hace el código intercambiable. Es la idea central. El ViewModel recibe un MarsPhotosRepository (la interfaz), así que puedes sustituir la implementación real por otra, por ejemplo una falsa para pruebas. Por eso el proyecto final trae FakeNetworkMarsPhotosRepository y FakeMarsApiService.

3. La inyección de dependencias evita que las clases creen sus propios colaboradores. En lugar de que el ViewModel haga NetworkMarsPhotosRepository() por dentro, se le entrega ya construido. Esto reduce el acoplamiento y hace el código más fácil de probar y de mantener.

4. AppContainer es el lugar donde se arma el grafo de dependencias. DefaultAppContainer crea Retrofit, el servicio de la API y el repositorio una sola vez y de forma perezosa (by lazy), y todos los comparten. Es una forma manual y simple de hacer lo que librerías como Hilt o Koin automatizan.

5. La clase Application es el punto de entrada del contenedor. MarsPhotosApplication crea el contenedor al iniciar la app. Así vive tanto como el proceso, y las pantallas lo consultan en lugar de construir cada cosa por su cuenta.

6. El ViewModel necesita una Factory cuando tiene parámetros. Como ahora recibe el repositorio en el constructor, el sistema no sabe cómo crearlo por sí solo. Hay que darle una ViewModelProvider.Factory que lo construya tomando el repositorio del contenedor.

7. El resultado práctico es una arquitectura por capas. La interfaz de usuario (Compose) habla con el ViewModel, el ViewModel con el repositorio y el repositorio con la fuente de datos. Cada capa tiene una responsabilidad clara, que es la base de la arquitectura recomendada por Android.
