import csv
import os
from datetime import datetime, timedelta

def validar_fecha(fecha):
    try:
        datetime.strptime(fecha, "%d/%m/%Y")
        return True
    except:
        return False

def validar_email(email):
    return "@" in email and "." in email

contratos = []
seguimientos = []

#REGISTRAR CONTRATO

def r_contrato():
    numero = input("Número de contrato: ")
    for i in contratos:
        if i ["numero"] == numero:
            print("Ese número ya existe.")
            return
    contratista = input("Nombre del contratista: ")
    objeto = input("Objeto del contrato: ")

    fecha_inicio = input("Fecha inicio (dd/mm/yyyy): ")
    if not validar_fecha(fecha_inicio):
        print("Fecha inválida")
        return
    fecha_fin = input("Fecha fin (dd/mm/yyyy): ")
    if not validar_fecha(fecha_fin):
        print("Fecha inválida")
        return

    if datetime.strptime(fecha_inicio,"%d/%m/%Y") > datetime.strptime(fecha_fin,"%d/%m/%Y"):
        print("La fecha de inicio no puede ser mayor que la final.")
        return
    
    valor = float(input("Valor del contrato: "))
    try:
        if valor <= 0:
            print("Valor inválido")
            return
    except:
        print("Debe ingresar número válido")
        return
    supervisor=input("Ingrese el nombre:")

    estado = input("Estado (ACTIVO/SUSPENDIDO/TERMINADO): ").upper()
    if estado not in {"ACTIVO","SUSPENDIDO","TERMINADO"}:
        print("Estado inválido")
        return

    correo = input("Correo: ")
    if not validar_email(correo):
        print("Correo inválido")
        return
    contrato = {
        "numero": numero,
        "contratista": contratista,
        "objeto": objeto,
        "fecha_inicio": fecha_inicio,
        "fecha_fin": fecha_fin,
        "valor": valor,
        "supervisor": supervisor,
        "estado": estado,
        "correo": correo
    }

    contratos.append(contrato)
    print("Contrato registrado correctamente.")

#LISTAR CONTRATOS

def listar_contratos():

    if len(contratos) == 0:
        print("No hay contratos registrados.")
        return

    for c in contratos:

        print("\n-------------------")
        print("Numero:", c["numero"])
        print("Contratista:", c["contratista"])
        print("Objeto:", c["objeto"])
        print("Fecha inicio:", c["fecha_inicio"])
        print("Fecha fin:", c["fecha_fin"])
        print("Valor:", c["valor"])
        print("Supervisor:", c["supervisor"])
        print("Estado:", c["estado"])
        print("Correo:", c["correo"])

#BUSCAR CONTRATO

def buscar_contrato():

    numero = input("Número de contrato: ")

    encontrado = False

    for c in contratos:

        if c["numero"] == numero:

            print("\nContrato encontrado")
            print("Numero:", c["numero"])
            print("Contratista:", c["contratista"])
            print("Objeto:", c["objeto"])
            print("Fecha inicio:", c["fecha_inicio"])
            print("Fecha fin:", c["fecha_fin"])
            print("Valor:", c["valor"])
            print("Supervisor:", c["supervisor"])
            print("Estado:", c["estado"])
            print("Correo:", c["correo"])

            encontrado = True

    if encontrado == False:
        print("Contrato no encontrado")
    

# REGISTRAR SEGUIMIENTO

def registrar_seguimiento():

    numero = input("Número de contrato: ")

    existe = False

    for c in contratos:
        if c["numero"] == numero:
            existe = True

    if not existe:
        print("El contrato no existe")
        return

    fecha = input("Fecha seguimiento (dd/mm/yyyy): ")

    if not validar_fecha(fecha):
        print("Fecha inválida")
        return

    descripcion = input("Descripción: ")

    try:
        avance = int(input("Avance (0-100): "))
        if avance < 0 or avance > 100:
            print("Avance inválido")
            return
    except:
        print("Debe ingresar un número")
        return

    observacion = input("Observación: ")

    seguimiento = {
        "numero": numero,
        "fecha": fecha,
        "descripcion": descripcion,
        "avance": avance,
        "observacion": observacion
    }

    seguimientos.append(seguimiento)

    print("Seguimiento agregado")
 
#LISTAR SEGUIMIENTOS
def listar_seguimientos():

    numero = input("Número de contrato: ")

    encontrado = False

    for s in seguimientos:

        if s["numero"] == numero:

            print("\n--------------")
            print("Numero contrato:", s["numero"])
            print("Fecha:", s["fecha"])
            print("Descripcion:", s["descripcion"])
            print("Avance:", s["avance"])
            print("Observacion:", s["observacion"])

            encontrado = True

    if encontrado == False:
        print("No hay seguimientos para ese contrato")

# ESTADISTICAS

def estadisticas():

    if len(contratos) == 0:
        print("No hay contratos")
        return

    total = 0
    estados = {"ACTIVO":0,"SUSPENDIDO":0,"TERMINADO":0}

    for c in contratos:
        total = total + c["valor"]
        estados[c["estado"]] = estados[c["estado"]] + 1

    promedio = total / len(contratos)

    mayor = contratos[0]
    menor = contratos[0]

    for c in contratos:
        if c["valor"] > mayor["valor"]:
            mayor = c
        if c["valor"] < menor["valor"]:
            menor = c

    print("\nTotal contratos:", len(contratos))
    print("Valor total contratado:", total)
    print("Promedio:", promedio)

    print("Contrato mayor valor:", mayor["numero"])
    print("Contrato menor valor:", menor["numero"])

    print("\nContratos por estado")

    for e in estados:
        print(e,":",estados[e])

    hoy = datetime.today()

    print("\nContratos próximos a vencer")

    for c in contratos:
        fecha_fin = datetime.strptime(c["fecha_fin"],"%d/%m/%Y")

        if fecha_fin >= hoy and fecha_fin <= hoy + timedelta(days=30):
            print(c["numero"], "-", c["contratista"], "-", c["fecha_fin"])

#GUARDAR CSV

def guardar_csv():

    if len(contratos) > 0:

        with open("contratos.csv", "w", newline="") as archivo:

            campos = contratos[0].keys()
            writer = csv.DictWriter(archivo, fieldnames=campos)

            writer.writeheader()

            for c in contratos:
                writer.writerow(c)


    if len(seguimientos) > 0:

        with open("seguimientos.csv", "w", newline="") as archivo:

            campos = seguimientos[0].keys()
            writer = csv.DictWriter(archivo, fieldnames=campos)

            writer.writeheader()

            for s in seguimientos:
                writer.writerow(s)

    print("Datos guardados")

def menu():

    while True:

        print("\n--- SISTEMA DE CONTRATOS ---")
        print("1. Registrar contrato")
        print("2. Listar contratos")
        print("3. Buscar contrato")
        print("4. Registrar seguimiento")
        print("5. Ver seguimientos")
        print("6. Estadísticas")
        print("7. Exportar CSV")
        print("8. Salir")

        opcion = input("Seleccione una opción: ")

        if opcion == "1":
            r_contrato()

        elif opcion == "2":
            listar_contratos()

        elif opcion == "3":
            buscar_contrato()

        elif opcion == "4":
            registrar_seguimiento()

        elif opcion == "5":
            listar_seguimientos()

        elif opcion == "6":
            estadisticas()

        elif opcion == "7":
            guardar_csv()

        elif opcion == "8":
            guardar_csv()
            print("Sistema finalizado")
            print("Total contratos:", len(contratos))
            break

        else:
            print("Opción inválida")


menu()
