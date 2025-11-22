classDiagram
    %% Roles
    class Usuario {
      +id: String
      +nombre: String
      +email: String
      +tipo: String  // cliente, comercio, admin
      +registrarCuenta()
      +iniciarSesion()
    }

    class Empleado {
      +id: String
      +nombre: String
      +rol: String  // inventarios, recepción, transporte
      +asignarTarea()
      +registrarActividad()
    }

    class Repartidor {
      +id: String
      +nombre: String
      +vehiculoId: String
      +estado: String  // disponible, en_ruta
      +aceptarRuta(rutaId)
      +actualizarEstado()
    }

    %% Núcleo de operaciones
    class Inventario {
      +id: String
      +restauranteId: String
      +capacidad: Number
      +actualizadoEn: Date
      +agregarProducto(productoId, cantidad)
      +retirarProducto(productoId, cantidad)
      +consultarStock(productoId)
    }

    class Producto {
      +id: String
      +restauranteId: String
      +nombre: String
      +descripcion: String
      +categoria: String
      +precio: Number
      +cantidadDisponible: Number
      +fechaCaducidad: Date
      +publicarExcedente()
      +marcarVendido(cantidad)
    }

    class Recepcion {
      +id: String
      +empleadoId: String
      +restauranteId: String
      +fecha: Date
      +estado: String  // recibido, rechazado
      +registrarRecepcion(productoId, cantidad)
      +validarCalidad(productoId)
    }

    class Distribucion {
      +id: String
      +origenRestauranteId: String
      +destinoId: String  // comercio o cliente
      +tipoDestino: String  // comercio, personal
      +fechaProgramada: Date
      +estado: String  // planificado, en_transito, entregado
      +planificar(productos: List)
      +asignarTransporte(transporteId)
      +generarRuta()
    }

    class Entrega {
      +id: String
      +distribucionId: String
      +fecha: Date
      +estado: String  // pendiente, completada, fallida
      +confirmarRecepcion(destinoId, firma)
      +reportarIncidencia(descripcion)
    }

    class Transporte {
      +id: String
      +tipo: String  // moto, auto, camioneta
      +capacidad: Number
      +placas: String
      +estado: String  // disponible, mantenimiento
      +programarMantenimiento(fecha)
      +asignarRepartidor(repartidorId)
    }

    class Ruta {
      +id: String
      +origen: String
      +destinos: List~String~
      +distanciaKm: Number
      +tiempoEstimadoMin: Number
      +optimizar()
      +recalcularPorTráfico()
    }

    class Reparto {
      +id: String
      +distribucionId: String
      +rutaId: String
      +repartidorId: String
      +transporteId: String
      +estado: String  // asignado, en_ruta, completado
      +iniciar()
      +actualizarProgreso()
      +completar()
    }

    class ValoracionServicio {
      +id: String
      +destinoId: String  // cliente o comercio
      +pedidoId: String
      +calificacion: Number  // 1-5
      +comentario: String
      +fecha: Date
      +registrar()
      +promedioPorRestaurante(restauranteId)
    }

    %% Apoyo comercial
    class Pedido {
      +id: String
      +usuarioId: String
      +restauranteId: String
      +fecha: Date
      +estado: String  // creado, pagado, en_preparacion, enviado, entregado
      +total: Number
      +agregarItem(productoId, cantidad)
      +calcularTotal()
      +marcarPagado()
    }

    class ItemPedido {
      +id: String
      +pedidoId: String
      +productoId: String
      +cantidad: Number
      +precioUnitario: Number
      +subtotal(): Number
    }

    class Pago {
      +id: String
      +pedidoId: String
      +metodo: String  // tarjeta, efectivo, transferencia
      +monto: Number
      +fecha: Date
      +estado: String  // pendiente, aprobado, fallido
      +procesar()
      +reembolsar()
    }

    class Restaurante {
      +id: String
      +nombre: String
      +direccion: String
      +horario: String
      +contacto: String
      +publicarProductoExcedente(productoId)
      +actualizarInventario()
    }

    class Comercio {
      +id: String
      +nombre: String
      +direccion: String
      +contacto: String
      +solicitarDistribucion(pedidoId)
    }

    %% Relaciones
    Usuario "1" --> "many" Pedido : realiza
    Pedido "1" --> "many" ItemPedido : contiene
    Pedido "1" --> "1" Pago : pago
    Pedido "many" --> "1" Restaurante : a_restaurante
    ItemPedido "many" --> "1" Producto : producto

    Restaurante "1" --> "1" Inventario : gestiona
    Inventario "1" --> "many" Producto : contiene
    Empleado "many" --> "1" Restaurante : trabaja_en
    Empleado "1" --> "many" Recepcion : registra

    Distribucion "1" --> "many" Producto : incluye
    Distribucion "1" --> "1" Ruta : usa
    Distribucion "1" --> "1" Transporte : asigna

    Reparto "1" --> "1" Distribucion : de
    Reparto "1" --> "1" Ruta : sigue
    Reparto "1" --> "1" Repartidor : ejecuta
    Reparto "1" --> "1" Transporte : utiliza
    Entrega "1" --> "1" Distribucion : completa

    ValoracionServicio "many" --> "1" Pedido : sobre
    Comercio "many" --> "many" Distribucion : solicita
    Repartidor "many" --> "many" Ruta : recorre
