Redaccion mia mia
 Funcionamiento master

la idea que tengo con la master (por cuerto el master es como un catalogo con mas cosas, en lo referido a que tambien debes ser capaz de visualizar las imagenes con los detalles de los productos, categorias, subcategorias, modelo, cantidad total, colores y cantidad disponible por colores, detalles extra, precio, etc, o sea si algo cuando se vea en la pantalla los distintos modelos no haya tanta informacion, quizas solo modelo, cantidad total y precio pero si se presiona en el modelo ya salgan todos los detalles)es que esta aplicacion sea el punto de entrada y de salida de los productos, asi como la que lleve a cabo el tema de analisis de datos. Por ejemplo se le mete un producto y se guarda en base de datos, cuando un comprador revisa el catalogo, ve el producto que desea comprar y contacta por whatsap, telegram o lo que sea dice que lo quiere comprar, yo vaya al master le ponga que objeto se va a comprar y en que cantidad, precio por el que se vendio(por si variase???) algunos datos del comprador como nombre, numero de telefono y direccion donde se envio el producto y este pase a una fase como de reserva(en esta fase de reserva el objeto se puede quitar y poner ademas lo que si tiene que pasar es que por ejemplo en las partes donde se veria la cantidad de ese modelo se le descuente los que haya en reserva, si al final se cancela la reserva se devuelve esta cantidad y si se valida la compra se elimina permanentemente y se le pasan los datos pertinentes a la base de datos para el tema de data science) y cuando se envie el producto y se haga la confirmacion de que se vendio se elimine ese producto de la reserva y quede como vendido.
en cuba hay muchas monedas asi que cuando se vea el precio se debe poder, en el área de reservas cuando se seleccione vender, salga un cartel que diga moneda , y ya antes de venderse se elija la moneda
también como una forma de verificar que no haya robo de dinero se debe poder hacer como un listado de ventas del dia, para esto crear un partado de estadísticas donde se vea estadísticas de ventas del dia, semana, mes

 Redacción de lo mismo De chatgpt, proyecto maybel funcionamiento del master


Sistema Master – Descripción Funcional y Operativa
Proyecto: Maybel
1. Rol del Sistema Master
El Sistema Master constituye el núcleo operativo central del proyecto Maybel.
Su función principal es actuar como punto único de entrada, control y salida de los productos, así como fuente primaria de datos para el análisis comercial y estadístico.
Conceptualmente, el Master puede entenderse como un catálogo ampliado, que no solo permite visualizar productos, sino que integra funciones críticas de gestión interna, tales como:
•	Administración de inventario
•	Gestión de reservas y ventas
•	Registro de información de compradores
•	Control de estados de los productos
•	Generación de datos históricos para análisis (Data Science)
El Master es la fuente de verdad del sistema:
todo producto, reserva, venta o modificación debe originarse y validarse en este módulo.
________________________________________
2. Visualización y navegación dentro del Master
La interfaz del Master se organiza en múltiples vistas o secciones, permitiendo al operador decidir qué acción realizar en cada momento (gestión de productos, reservas, análisis, etc.).
En particular, la navegación de productos sigue una estructura jerárquica clara:
1.	Selección de categoría
2.	Visualización de subcategorías
3.	Listado de modelos
4.	Acceso a la vista detallada del producto seleccionado
Este enfoque permite escalar el catálogo sin perder claridad ni usabilidad.
________________________________________
3. Visualización de productos
Para evitar la sobrecarga de información y facilitar la operación diaria, el sistema presenta los productos en dos niveles de visualización.
3.1 Vista resumida (lista de modelos)
En la vista principal del inventario, los productos se muestran de forma compacta, incluyendo únicamente la información esencial:
•	Modelo / nombre del producto
•	Categoría y subcategoría
•	Cantidad total disponible
•	Precio base
Esta vista permite:
•	Identificación rápida del inventario
•	Operaciones ágiles de búsqueda y control
•	Reducción de errores por exceso de información
________________________________________
3.2 Vista detallada (detalle del producto)
Al seleccionar un modelo específico, el sistema despliega una vista expandida con toda la información asociada al producto, incluyendo:
•	Descripción detallada
•	Imágenes del producto
•	Categoría y subcategoría
•	Precio base
•	Cantidad total
•	Variantes (por ejemplo: colores, versiones o modelos secundarios)
•	Cantidad disponible por variante
•	Detalles adicionales relevantes
La separación entre vista resumida y vista detallada mejora significativamente la experiencia de uso y la precisión operativa.
________________________________________
4. Gestión de inventario y flujo de venta
4.1 Entrada de productos
El Master es el único sistema autorizado para el registro de nuevos productos.
Durante el alta de un producto se definen:
•	Características básicas
•	Categoría y subcategoría
•	Imágenes asociadas
•	Stock inicial
•	Variantes (colores, modelos, etc.)
Una vez registrado, el producto queda disponible:
•	En el Master (gestión interna)
•	En el catálogo público (visualización para compradores)
________________________________________
4.2 Proceso de interés del comprador
El inicio del proceso de venta ocurre fuera del sistema, a través de canales de comunicación directa como:
•	WhatsApp
•	Telegram
•	Contacto personal
El catálogo público actúa únicamente como herramienta de consulta, mientras que toda la operación formal se registra en el Master.
________________________________________
4.3 Creación de una reserva
Cuando un comprador manifiesta interés en adquirir un producto, el operador registra la operación creando una reserva dentro del Master.
La reserva incluye:
•	Producto y variante seleccionada
•	Cantidad solicitada
•	Precio de venta acordado (puede diferir del precio base)
•	Datos del comprador:
o	Nombre
o	Teléfono
o	Dirección de entrega
•	Fecha y estado de la reserva (generados automáticamente mediante timestamp)
________________________________________
5. Sistema de estados del producto
Para garantizar consistencia del inventario y trazabilidad completa, los productos y sus unidades siguen un modelo de estados controlado.
5.1 Estados definidos
1.	Disponible
o	El producto forma parte del inventario libre
o	Se muestra como disponible en el catálogo
2.	Reservado
o	El producto está comprometido a un comprador
o	La cantidad reservada se descuenta del stock disponible
o	El producto no se elimina del inventario
3.	Vendido
o	La venta se confirma
o	El producto sale definitivamente del inventario
o	Se registra como venta efectiva
4.	Reserva cancelada
o	La reserva se anula
o	La cantidad vuelve a estar disponible en el inventario
________________________________________
6. Impacto de las reservas en el inventario
El sistema mantiene coherencia automática entre reservas y stock:
•	Al crear una reserva:
o	La cantidad reservada se descuenta del stock visible
•	Al cancelar una reserva:
o	La cantidad se reincorpora al stock disponible
•	Al confirmar la venta:
o	La reserva se cierra
o	El producto se marca como vendido
Este mecanismo evita sobreventas y asegura consistencia entre el Master y el catálogo público.
________________________________________
7. Confirmación de venta y cierre de operación
Una vez que el producto es entregado y la venta confirmada:
•	La reserva se convierte en venta definitiva
•	Se registra la fecha de venta (timestamp del sistema)
•	La operación queda cerrada a nivel de inventario
Toda la información pasa a formar parte del histórico para análisis posterior.
________________________________________
8. Integración con análisis de datos (Data Science)
El Master funciona como fuente principal de datos analíticos, permitiendo generar métricas como:
•	Productos más vendidos
•	Tiempo promedio en estado de reserva
•	Diferencias entre precio base y precio real de venta
•	Ventas por categoría y subcategoría
•	Preferencias por variantes (color, modelo, etc.)
Estos datos permiten optimizar:
•	Estrategias de precios
•	Gestión de inventario
•	Toma de decisiones comerciales
________________________________________
9. Mejoras conceptuales recomendadas (opcionales)
9.1 Separación entre “Producto” y “Unidad de venta”
Ejemplo:
•	Producto: Bicicleta MTB Aro 26
•	Unidad: Bicicleta MTB Aro 26 – Color Rojo
Esto facilita:
•	Reservas parciales
•	Control por variante
•	Análisis más detallado
________________________________________
9.2 Registro de historial de cambios
Se recomienda almacenar:
•	Cambios de precio (especialmente relevante en contextos de alta variación monetaria)
•	Cambios de estado
•	Cancelaciones de reservas
Esto mejora la trazabilidad y el valor analítico de los datos.
________________________________________
9.3 Expiración de reservas (opcional)
•	Reservas con tiempo límite (por ejemplo, una semana)
•	Liberación automática del stock si no se confirma la venta
________________________________________
Cosas extras que añadir
en cuba hay muchas monedas asi que cuando se vea el precio se debe poder, en el área de reservas cuando se seleccione vender, salga un cartel que diga moneda , y ya antes de venderse se elija la moneda
también como una forma de verificar que no haya robo de dinero se debe poder hacer como un listado de ventas del dia, para esto crear un partado de estadísticas donde se vea estadísticas de ventas del dia, semana, mes


