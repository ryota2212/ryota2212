from socket import *
import os
import re

def exists(identificador):
    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
    archivo_propiedades = os.path.join(ruta_directorio_actual, "propiedades.txt")
    with open(archivo_propiedades, "r") as propiedades_file:
        for linea in propiedades_file:
            if "id: {}".format(identificador) in linea:
                return True
    return False

def crearDirectorio():
    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
    archivo_propiedades = os.path.join(ruta_directorio_actual, "propiedades.txt")

    if not os.path.exists(archivo_propiedades):
        open(archivo_propiedades, "w").close()

def aggPropiedad(clienteSocket):
    crearDirectorio()
    identificador = input("Ingrese el numero de identificador de la propiedad: ")
    direccion = input("Ingrese la direccion de la propiedad: ")
    telefono = input("Ingrese el telefono: ")
    numeroAlcobas = input("Ingrese el numero de alcobas: ")
    areaTotal = input("Ingrese el area total de la propiedad: ")
    valorCompra = input("Ingrese el valor de compra de la propiedad: ")
    valorVenta = input("Ingrese el valor de venta de la propiedad: ")
    valorArrendamiento = input("Ingrese el valor de arrendamiento de la propiedad: ")
    conestado = input("Ingrese el estado de la propiedad:\n1. Arrendada\n2. Desocupada\ningrese valor numerico 1 o 2 \n")

    if exists(identificador):
        print("La propiedad con el identificador ya existe en el archivo.")
    else:
        if conestado == "1":
            estado = "Arrendada"
        elif conestado == "2":
            estado = "Desocupada"
        else:
            print("Respuesta inválida.")
            return

        data = f"\n\nid: {identificador}\nDireccion de la propiedad: {direccion}\nTelefono: {telefono}\nNúmero de alcobas: {numeroAlcobas}\nArea total: {areaTotal}mts\nValor de compra: ${valorCompra}\nValor de venta: ${valorVenta}\nValor de arrendamiendo: ${valorArrendamiento}\nEstado: {estado}"
        guardar_propiedad(data)
        clienteSocket.send(data.encode('utf-8'))
        response = clienteSocket.recv(1024).decode('utf-8')
        print("Respuesta del servidor:", response)

def guardar_propiedad(data):
    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
    archivo_propiedades = os.path.join(ruta_directorio_actual, "propiedades.txt")
    with open(archivo_propiedades, "a") as propiedades_file:
        propiedades_file.write(data)

def crearArchivoNovedad():
    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
    archivo_novedades = os.path.join(ruta_directorio_actual, "novedades.txt")

    if not os.path.exists(archivo_novedades):
        open(archivo_novedades, "w").close()


def agregarNovedad(clienteSocket):
    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
    archivo_novedades = os.path.join(ruta_directorio_actual, "novedades.txt")
    identificador = input("Ingrese el identificador de la propiedad que desea actualizar: ")
    if exists(identificador):
        crearArchivoNovedad()
        tipoTransaccion = input("Ingrese el tipo de transacción que se realizó:\n1. Se arrendó\n2. Se compró\n3. Se vendió: \n")
        if tipoTransaccion == "1":
            tipo_transaccion = "Se arrendó"
        elif tipoTransaccion == "2":
            tipo_transaccion = "Se compró"
        elif tipoTransaccion == "3":
            tipo_transaccion = "Se vendió"

        data = f"\n-----NUEVA NOVEDAD ----\nid: {identificador}\nActualización: {tipo_transaccion}"
        #registrarNovedad(data)
        actualizarPropiedad(identificador, tipo_transaccion)
        #clienteSocket.send(data.encode('utf-8'))
        #response = clienteSocket.recv(1024).decode('utf-8')
        #print("Respuesta del servidor:", response)
    else:
        print("No existe ninguna propiedad con este identificador.")

        #creo que no hace nada
def actualizarDatosPropiedad(id_propiedad, tipo_transaccion):
    with open('propiedades.txt', 'r') as file:
        lineas = file.readlines()

    for i in range(len(lineas)):
        if f"id: {id_propiedad}" in lineas[i]:
            datos_propiedad = lineas[i:i+10]  # Extraer los datos de la propiedad (10 líneas)
            datos_propiedad[9] = f"Estado: {'Desocupada' if tipo_transaccion != 'Se arrendó' else 'Arrendada'}\n"
            lineas[i:i+10] = datos_propiedad  # Actualizar los datos de la propiedad en las líneas
            break

    with open('propiedades.txt', 'w') as file:
        file.writelines(lineas)

def actualizarPropiedad(identificador, tipo_transaccion):
    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
    archivo_propiedades = os.path.join(ruta_directorio_actual, "propiedades.txt")
    
    with open(archivo_propiedades, "r") as propiedades_file:
        lineas_propiedades = propiedades_file.readlines()
   
    for i in range(len(lineas_propiedades)):
        if f"id: {identificador}" in lineas_propiedades[i]:
            if tipo_transaccion == "Se vendió":
                estado = "Desocupada"
                lineas_propiedades[i+8] = f"Estado: Desocupada\n"
                prec_compra = lineas_propiedades[i+5]
                valor = re.search(r"\$\d+", prec_compra)
                if valor:
                    valor_numerico = int(valor.group().replace("$", ""))
                    print(valor_numerico)

                
    #with open(archivo_propiedades, "w") as propiedades_file:
        #propiedades_file.writelines(lineas_propiedades)


def registrarNovedad(data):
    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
    archivo_novedades = os.path.join(ruta_directorio_actual, "novedades.txt")

    with open(archivo_novedades, "a") as novedades_file:
        novedades_file.write(data)

        #cambiarlos
##def guardarTotalVendido(total_vendido):
##    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
##    archivo_propiedades = os.path.join(ruta_directorio_actual, "propiedades.txt")
##    with open(archivo_propiedades, "a") as propiedades_file:
##        propiedades_file.write("\n\n-----VALORES TOTALES-----\n")
##        propiedades_file.write(f"Valor total de las propiedades vendidas: ${total_vendido}\n")

##def obtenerTotalArrendado():
##    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
##    archivo_novedades = os.path.join(ruta_directorio_actual, "novedades.txt")
##    total_arrendado = 0
##    with open(archivo_novedades, "r") as novedades_file:
##        for linea in novedades_file:
##            if "Arrendada" in linea:
##                total_arrendado += 1
##    return total_arrendado
##
        #este lo tengo que cambiar
##def guardarTotalArrendado(total_arrendado):
##    ruta_directorio_actual = os.path.dirname(os.path.abspath(__file__))
##    archivo_propiedades = os.path.join(ruta_directorio_actual, "propiedades.txt")
##    with open(archivo_propiedades, "a") as propiedades_file:
##        propiedades_file.write(f"Valor total de las propiedades arrendadas: {total_arrendado}\n")

def menu():
    clienteSocket = socket(AF_INET, SOCK_STREAM)
    clienteSocket.connect(("localhost", 12000))
    opcion = int(input("\n¡Bienvenido/a! ¿Qué desea realizar? \n1. Agregar una propiedad"
                       + "\n2. Actualizar una propiedad \n3. Salir \n"))
    if opcion == 1:
        aggPropiedad(clienteSocket)
        clienteSocket.close()
        menu()
    else:
        if opcion == 2:
            agregarNovedad(clienteSocket)
            menu()
        elif opcion == 3:
            print("Ha finalizado la conexión con el servidor")
            clienteSocket.close()
        else:
            print("La opción digitada no es correcta. Intente nuevamente")
            menu()
            #agregar mas opciones

print("Conexión establecida con el servidor")
menu()
