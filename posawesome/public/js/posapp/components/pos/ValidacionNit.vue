<template>



  <v-row justify="center">

    <v-dialog v-model="customerDialog" max-width="600px" persistent>


      <v-card>

        <v-overlay :value="visible_carga">
          <v-progress-circular indeterminate size="64"></v-progress-circular>
        </v-overlay>

        <v-card-title>
          <span class="headline indigo--text">{{ __('BUSCAR CLIENTE') }}</span>

        </v-card-title>

        <v-alert :type="tipo_alerta" :value="displayError" dismissible v-if="mostrarAlerta">{{ displayError }}
        </v-alert>

        <v-card-text class="pa-0">
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field dense color="indigo" :label="frappe._('Ingrese el numero de Nit')"
                  background-color="white" v-model="nit_cliente" :disabled="disable_nit_cliente"
                  :rules="nit_clienteRules" required>
                </v-text-field>
              </v-col>
            </v-row>

            <v-row v-if="visible_cfCliente">
              <v-col cols="6">
                <v-autocomplete clearable dense auto-select-first color="indigo" :label="frappe._('Cliente')"
                  v-model="cfCliente" :items="cfClientes" item-value="name" item-text="customer_name" return-object
                  background-color="white" :no-data-text="__('Cliente no encontrado')" :rules="cliente_cfRules">
                </v-autocomplete>
              </v-col>
            </v-row>

            <v-row v-if="visible_cfCliente">
              <v-col cols="2">
                <v-btn color="primary" dark @click="btn_nuevo_cliente_cf">{{
                    __('Nuevo Cliente')
                }}</v-btn>
              </v-col>
            </v-row>

            <v-row v-if="visible_customer_name">
              <v-col cols="12">
                <v-text-field dense color="indigo" :label="frappe._('Nombre registrado:')" background-color="white"
                  v-model="customer_name" :disabled="disable_customer_name" :rules="customer_nameRules">
                </v-text-field>
              </v-col>
            </v-row>
            <v-row v-if="visible_direccion">
              <v-col cols="12">
                <v-text-field dense color="indigo" :label="frappe._('Direccion:')" background-color="white"
                  v-model="direccion" :rules="direccionRules" required></v-text-field>
              </v-col>
            </v-row>
            <v-row v-if="visible_telefono">
              <v-col cols="12">
                <v-text-field dense color="indigo" :label="frappe._('Telefono:')" background-color="white"
                  v-model="telefono" :rules="telefonoRules" type="number" required @change="mostrarBotonActualizar">
                </v-text-field>
              </v-col>
            </v-row>
            <v-row v-if="visible_correo">
              <v-col cols="12">
                <v-text-field dense color="indigo" :label="frappe._('Correo:')" background-color="white"
                  v-model="correo" :rules="correoRules" required @change="mostrarBotonActualizar"></v-text-field>
              </v-col>
            </v-row>

            <v-row v-if="visible_departamento">
              <v-col cols="6">
                <v-autocomplete clearable dense auto-select-first color="indigo" :label="frappe._('Departamento')"
                  v-model="departamento" :items="departamentos" background-color="white"
                  no-data-text="__('Departamento no encontrado')" :rules="departamentoRules" @blur="getMunicipios">
                </v-autocomplete>
              </v-col>

              <v-col cols="6">
                <v-autocomplete clearable dense auto-select-first color="indigo" :label="frappe._('Municipio')"
                  v-model="municipio" :items="municipios" background-color="white" :rules="municipioRules"
                  v-if="visible_municipio">
                </v-autocomplete>
              </v-col>
            </v-row>

            <v-row v-if="visible_referencia">
              <v-col cols="6">
                <v-autocomplete clearable dense auto-select-first color="indigo" :label="frappe._('Referencia')"
                  v-model="referencia" :items="referencias" background-color="white" :rules="referenciaRules">
                </v-autocomplete>
              </v-col>

              <v-col cols="6">
                <v-autocomplete clearable dense auto-select-first color="indigo" :label="frappe._('Grupo de Cliente')"
                  v-model="grupocliente" :items="grupoclientes" background-color="white" :rules="grupoRules"
                  v-if="visible_grupocliente">
                </v-autocomplete>
              </v-col>
            </v-row>


          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn color="error" dark @click="close_dialog" v-if="visible_btn_cerrar">{{
              __('Cerrar')
          }}</v-btn>


          <v-btn color="primary" dark @click="submit_dialog" v-if="visible_btn_buscar">{{
              __('Buscar')
          }}</v-btn>


          <v-btn color="primary" dark @click="btn_guardar_cliente" v-if="visible_btn_guardar">{{
              __('Guardar')
          }}</v-btn>

          <v-btn color="primary" dark @click="btn_actualizar" v-if="visible_btn_actualizar">{{
              __('Actualizar')
          }}</v-btn>


          <v-btn color="primary" dark @click="btn_Confirmar_cfCliente" v-if="visible_cfCliente">{{
              __('Confirmar')
          }}</v-btn>




        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>



</template>
<style scoped>
.v-progress-circular {
  margin: 1rem;
}
</style>

<script>
import { evntBus } from '../../bus';
export default {
  data: () => ({
    visible_carga: false,
    customerDialog: false,

    customer_name: '',
    telefono: '',
    correo: '',
    direccion: '',
    departamento: '',
    departamentos: [],
    municipio: '',
    municipios: [],
    customer_primary_key: '',

    /**Actualizar */

    customer_name_anterior: '',
    telefono_anterior: '',
    correo_anterior: '',

    /*Nuevo Cliente*/
    referencia: '',
    referencias: [],
    grupocliente: '',
    grupoclientes: [],

    /*CF */
    cfCliente: '',
    cfClientes: [],

    /**Visibilidad campos */

    visible_grupocliente: false,
    visible_referencia: false,
    visible_municipio: false,
    visible_departamento: false,
    visible_correo: false,
    visible_mobile_no: false,
    visible_telefono: false,
    visible_customer_name: false,
    visible_cfCliente: false,
    visible_btn_guardar: false,
    visible_btn_buscar: true,
    visible_btn_cerrar: true,


    /**Enabled/Disabled campos */
    disable_nit_cliente: false,
    disable_customer_name: true,

    /***Visibilidad de secciones del modal***/
    visible_validacion_SAT: false,
    visible_encontro_cliente: false,
    visible_nuevo_cliente: false,
    visible_nit_cf: false,
    visible_btn_actualizar: false,

    /*Alerta*/
    mostrarAlerta: false,
    displayError: '',
    tipo_alerta: '',

    /*flags */

    flag_nit_validado_con_exito: false,

    /* RULES */


    telefonoRules: [
      v => !!v || 'Telefono es obligatorio',
      v => v.length <= 8 || 'Telefono debe ser de 8 caracteres',],

    correoRules: [
      v => !!v || 'Correo es obligatorio',
      v => /.+@.+/.test(v) || 'Correo debe ser valido',
    ],

    direccionRules: [
      v => !!v || 'Dirección es obligatorio'
    ],
    departamentoRules: [
      v => !!v || 'Departamento es obligatorio'
    ],
    municipioRules: [
      v => !!v || 'Municipio es obligatorio'
    ],
    referenciaRules: [
      v => !!v || 'Referencia es obligatorio'
    ],
    grupoRules: [
      v => !!v || 'Grupo de cliente es obligatorio'
    ],
    customer_nameRules: [
      v => !!v || 'Nombre de cliente es obligatorio'
    ],
    nit_clienteRules: [
      v => !!v || 'Nit es obligatorio'
    ],
    cliente_cfRules: [
      v => !!v || 'Seleccione el cliente'
    ]



  }),
  watch: {

    overlay(val) {
      val && setTimeout(() => {
        this.overlay = false
      }, 3000)
    }

  },
  methods: {

    visibleTodosCampos(p) {
      this.visible_grupocliente = p;
      this.visible_referencia = p;
      this.visible_municipio = p;
      this.visible_departamento = p;
      this.visible_correo = p;
      this.visible_telefono = p;
      this.visible_customer_name = p;
      this.visible_cfCliente = p;
      this.visible_direccion = p;
      this.visible_btn_guardar = p;
      this.visible_btn_actualizar = p;




    },

    visibleCamposNuevoCliente(p) {
      this.visible_grupocliente = p;
      this.visible_referencia = p;
      this.visible_btn_guardar = p;
      this.visible_btn_buscar = false;
      this.visible_carga = false;


    },

    visibleCfCampos(p) {
      this.visible_cfCliente = p;
      this.visible_btn_buscar = p;
      this.visible_carga = false;


    },

    visibleCamposComunes(p) {
      this.visible_municipio = p;
      this.visible_departamento = p;
      this.visible_correo = p;
      this.visible_telefono = p;
      this.visible_customer_name = true;
      this.visible_direccion = p;
      this.visible_carga = false;
    },

    visibleCamposActualizar() {


      this.visible_municipio = false;
      this.visible_departamento = false;
      this.visible_correo = true;
      this.visible_telefono = true;
      this.visible_customer_name = true;
      this.visible_direccion = false;
      //this.visible_btn_actualizar = true;
      this.visible_btn_buscar = false;
      this.visible_carga = false;

    },

    limpiarCampos() {
      this.customer_name = '';
      this.telefono = '';
      this.correo = '';
      this.direccion = '';
      this.departamento = '';
      this.municipio = '';
      this.nit_cliente = '';
      this.referencia = '';
      this.grupocliente = '';
      this.cfCliente = '';
      this.visible_carga = false;
    },

    close_dialog() {
      this.customerDialog = false;
      this.visible_carga = false;
    },

    btn_nuevo_cliente_cf() {
      this.mostrarAlerta = true;
      this.displayError = "Ingrese los datos del nuevo cliente con nit CF";
      this.tipo_alerta = "info";
      this.visibleCamposComunes(true);
      this.visibleCamposNuevoCliente(true);
      this.visibleCfCampos(false);
      this.disable_customer_name = false; //Es el unico caso en que es requerido el customer_name

    },

    btn_guardar_cliente() {



      let nuevoCliente = {
        "customer_name": this.customer_name,
        "mobile_no": this.telefono,
        "email": this.correo,
        "source": this.referencia,
        "group": this.grupocliente,
        "address": this.direccion,
        "mun": this.municipio,
        "depto": this.departamento,
      }
      console.log(nuevoCliente);
      this.createCustomer(nuevoCliente, 1, this.nit_cliente);
    },

    mostrarBotonActualizar() {
      console.log("Entro a boton actualizar");
      if (this.visible_btn_guardar == false) {
        this.visible_btn_actualizar = true;

      }

    },

    updateEmail(primaryContact, newEmail, customerName) {
      frappe.db.get_list('Contact', {
        fields: ["`tabContact Email`.`name`"],
        filters: [["Contact", "name", "=", primaryContact]],
        limit: 500,
      }).then(records => {
        console.log("INFORMACIÓN DE CONTACTO", records);
        frappe.db.set_value('Contact Email', records[0].name, {
          email_id: newEmail
        }).then(r => {
          console.log('Email Actualizado');
          frappe.db.set_value('Customer', customerName, {
            customer_primary_contact: primaryContact
          }).then(r => {
            console.log('Customer Actualizado');
            evntBus.$emit('show_mesage', {
              text: __('Cliente  actualizado  exitosamente.'),
              color: 'success',
            });

            frappe.utils.play_sound('submit');
          });
        });
      });
    },

    updateMobileNo(primaryContact, newMobile, newEmail, customerName) {

      console.log("Ingreso a updateMobileNo");
      console.log("primaryContact: " + primaryContact);
      console.log("newMobile: " + newMobile);
      console.log("newEmail: " + newEmail);
      console.log("customerName: " + customerName);

      frappe.db.get_list('Contact', {
        fields: ["`tabContact Phone`.`name`"],
        filters: [["Contact", "name", "=", primaryContact]],
        limit: 500,
      }).then(records => {
        console.log("records[0].name" + records[0].name);
        frappe.db.set_value('Contact Phone', records[0].name, {
          phone: newMobile
        }).then(r => {
          console.log('Phone Actualizado');
          this.updateEmail(primaryContact, newEmail, customerName)
        });
      });

    },

    updateContact(customer_name, new_mobile, new_email) {
      let vm = this;
      frappe.db.get_value('Customer', customer_name, 'customer_primary_contact')
        .then(r => {
          this.updateMobileNo(r.message.customer_primary_contact, new_mobile, new_email, customer_name);

          //Si el cliente existe en base de datos PERO no ha sido validado por la SAT debe actualizar la direccion con la direccion de la SAT y los campos de nit_verificado y nombre_comercial
          if (vm.flag_nit_validado_con_exito) {

            //Creamos la direccion
            this.getPostalCode(this.direccion, this.municipio, this.departamento, this.customer_primary_key);
            //Actualizamos el campo nit_verificado y nombre_comercial
            frappe.db.set_value('Customer', vm.customer_primary_key, {
              'nit_verificado': 1,
              'nombre_comercial': vm.customer_name,
              'customer_name':vm.customer_name
            });
          }
        })
    },

    validateChangeFieldsEmailAndMobile(valueEmail, lastValueEmail, valueMobile, lastValueMobile) {
      let existChange = (valueEmail != lastValueEmail || valueMobile != lastValueMobile) ? true : false
      return existChange;
    },

    btn_actualizar() {

      if (validateChangeFieldsEmailAndMobile(this.correo, this.correo_anterior, this.telefono, this.telefono_anterior)) {
        console.log("Si hay cambios.", validateChangeFieldsEmailAndMobile(this.correo, this.correo_anterior, this.telefono, this.telefono_anterior))
        this.updateContact(this.customer_primary_key, this.telefono, this.correo);        //updCustomer(records.name, nit, records.nit_verificado,records.nombre_comercial, email_id, mobile_no, 1);
        this.close_dialog();

      }

    },

    btn_Confirmar_cfCliente() {

      if (this.cfCliente == '') {
        this.mostrarAlerta = true;
        this.displayError = "Seleccione un cliente";
        this.tipo_alerta = "error";
        return;
      }


      this.customer_name = this.cfCliente.name;
      this.telefono = this.cfCliente.mobile_no;
      this.correo = this.cfCliente.email_id;
      //this.visibleCamposComunes(true); //Se comenta, actualmente no permite actualizar ningun campo cuando se selecciona un nit existente
      this.visibleCamposActualizar();
      this.visibleCfCampos(false);
      this.mostrarAlerta = true;
      this.displayError = "Cliente con nit CF seleccionado: " + this.cfCliente.name;
      this.tipo_alerta = "info";

      evntBus.$emit('set_customer', this.cfCliente.name);
      this.close_dialog();

    },

    getDepartamentos() {
      if (this.departamentos.length > 0) return;
      const vm = this;
      frappe.db
        .get_list('State', {
          fields: ['name'],
          filters: [["country", "=", "Guatemala"]],
          page_length: 1000,
          limit: 30
        })
        .then((data) => {
          if (data.length > 0) {
            data.forEach((el) => {
              vm.departamentos.push(el.name);
            });
          }
        });
    },

    getReferencias() {
      if (this.referencias.length > 0) return;
      const vm = this;
      frappe.db
        .get_list('Lead Source', {
          fields: ['name'],
          page_length: 1000,
          limit: 50
        })
        .then((data) => {
          if (data.length > 0) {
            data.forEach((el) => {
              vm.referencias.push(el.name);
            });
          }
        });
    },

    getGrupoClientes() {
      if (this.grupoclientes.length > 0) return;
      const vm = this;
      frappe.db
        .get_list('Customer Group', {
          fields: ['name'],
          page_length: 1000,
          limit: 50
        })
        .then((data) => {
          if (data.length > 0) {
            data.forEach((el) => {
              vm.grupoclientes.push(el.name);
            });
          }
        });
    },

    getClientesCF() {
      console.log("Entro a getClientesCF()");
      if (this.cfClientes.length > 0) return;
      const vm = this;
      frappe.db
        .get_list('Customer', {
          fields: ['name', 'customer_name', 'nit_verificado', 'mobile_no', 'email_id', 'nombre_comercial'],
          filters: [["nit_del_cliente", "in", ["CF", "cf", "Cf", "cF"]]],
          limit: 10000,

        })
        .then((data) => {
          if (data.length > 0) {
            data.forEach((el) => {
              //vm.cfClientes.push(el.name);
              vm.cfClientes.push(el);
            });
          }
        });
    },

    getMunicipios(event) {

      console.log("onchange event");
      console.log(event.target.value);

      //if (this.municipios.length > 0) return;
      const vm = this;
      console.log("1");

      frappe.db
        .get_list('County', {
          fields: ['name'],
          filters: [["state", "=", event.target.value]],
          page_length: 1000,
          limit: 30
        })
        .then((data) => {
          vm.municipios = [];
          if (data.length > 0) {
            data.forEach((el) => {
              vm.municipios.push(el.name);
            });
          }
        });
      console.log("2");

    },

    createCustomer(values, nitValidado, nit) {
      let nombreComercial

      console.log("Este es el nit del nuevo cliente", nit)

      if (nitValidado == 1) {
        nombreComercial = values.customer_name;
      } else {
        nombreComercial = '';
      }

      let vm = this;
      const args = {
        name: values.customer_name,
        customer_name: values.customer_name,
        company: '',
        tax_id: '',
        mobile_no: values.mobile_no,
        email_id: values.email,
        referral_code: '',
        birthday: '',
        customer_group: values.group,
        territory: '',
      };

      frappe.db.insert({
        doctype: 'Customer',
        nit_del_cliente: nit,
        nit_verificado: nitValidado,
        customer_name: values.customer_name,
        nombre_comercial: nombreComercial,
        customer_type: 'Individual',
        mobile_no: values.mobile_no,
        email_id: values.email,
        source: values.source,
        customer_group: values.group
      }).then(doc => {

        evntBus.$emit('show_mesage', {
          text: __('Cliente  creado  exitosamente.'),
          color: 'success',
        });

        frappe.utils.play_sound('submit');



        //updateFieldCustomerPOS(doc.name, doc.name)
        //console.log(doc)
        vm.getPostalCode(values.address, values.mun, values.depto, values.customer_name);
        evntBus.$emit('add_customer_to_list', args);
        evntBus.$emit('set_customer', args.name);
        vm.close_dialog();
      })

    },

    getPostalCode(direccion, county, state, customer) {

      let placeName = getPlaceName(direccion, county);
      let zone = getZoneAddress(direccion);
      let vm = this;
      $.ajax({
        url: 'https://hook.integromat.com/mot5mex9tzmyio1pfvvik9j84jvvy9gl',
        type: 'POST',
        data: {
          placename: placeName,
          countryCode: 'GT',
          maxRows: 1,
          username: 'creativelatam'
        },
        success: function (respuesta) {

          let response = JSON.parse(respuesta);
          let postalCode = response[0].postalCode;

          if (isEmpty(postalCode)) postalCode = '01010';

          vm.createAddress(direccion, zone, county, state, postalCode, customer);
          //updateFieldsFEL(direccion, county, state, postalCode);
          console.log('Código Postal', postalCode);

        },
        error: function () {
          console.log("No se ha podido obtener la información");
        }
      });
    },



    createAddress(direccion, zone, mun, depto, postal_code, customer) {


      console.log('%c%s', 'color: green;', '--- INICIANDO CREACION DE DIRECCION CLIENTE ---')
      frappe.db.insert({
        doctype: 'Address',
        address_title: customer,
        address_type: 'Billing',
        address_line1: direccion,
        zone: zone,
        city: mun,
        county: mun,
        state: depto,
        country: 'Guatemala',
        pincode: postal_code,
        is_primary_address: 1,
        links: [{
          link_doctype: 'Customer',
          link_name: customer
        }]
      }).then(doc => {

        console.log('DIRECCION CREADA', doc);
        frappe.show_alert('DIRECCIÓN CREADA', 10);
      })
    },


    submit_dialog() {

      if (this.nit_cliente == '') {
        this.mostrarAlerta = true;
        this.displayError = "Campo Nit es Obligatorio";
        this.tipo_alerta = "error";
        return;
      }
      this.visible_carga = true;



      this.visibleTodosCampos(false);
      this.mostrarAlerta = false;
      this.disable_nit_cliente = true;
      console.log("NIT A VALIDAR: " + this.nit_cliente);
      let nit = replaceSpaceSlashHyphen(this.nit_cliente)

      if (nit.toUpperCase() != 'CF') {
        //Le mandamos el nit tal como lo ingreso el vendedor
        this.existCustomerWithNIt(this.nit_cliente);
      } else {
        this.existCustomerWithCF(nit);
      }




    },

    existCustomerWithCF(nit) {
      this.visible_carga = false;
      this.visible_cfCliente = true;
      this.visible_btn_buscar = false;

    },

    existCustomerWithNIt(nit) {

      console.log("Entra a existCustomerWithNIt nit;" + nit);


      //Le quito los guiones aunque los traiga
      let nitSinGuion = replaceSpaceSlashHyphen(nit);
      let nitConGuion;

      //Si originalmente el nit no trae guiones se lo agrego
      if (!nit.includes("-")) {
        let nit1 = nit.substring(0, nit.length - 1);
        let nit2 = nit.substring(nit.length - 1, nit.length);
        nitConGuion = nit1 + "-" + nit2;

      } else {
        nitConGuion = nit;
      }

      console.log("nitConGuion=" + nitConGuion);
      console.log("nitSinGuion=" + nitSinGuion);

      let vm = this;
      frappe.db.get_list('Customer', {
        fields: ['name', 'customer_name', 'nit_verificado', 'mobile_no', 'email_id', 'nombre_comercial'],
        filters: [['Customer', 'nit_del_cliente', 'in', [nitConGuion, nitSinGuion]]], //Buscamos en la base de datos si existe el nit con guion y sin guion
        limit: 1000
      }).then(records => {

        console.log("CLIENTE", records);


        if (!isEmpty(records)) {
          console.log("YA EXISTE CLIENTE", records);


          //Obtengo solo el primer valor en caso de duplicado
          records = records[0];

          if (records.nit_verificado == 1) {

            //vm.visibleCamposComunes(true); //Se comenta para que se acople a como funciona actualmente, solo es visible el correo y telefono
            vm.visibleCamposActualizar();
            console.log("El nit del cliente existe en la base de datos y ya fue validado por la SAT");
            vm.telefono = records.mobile_no;
            vm.correo = records.email_id;
            vm.customer_name = records.customer_name;
            vm.customer_primary_key = records.name;
            vm.correo_anterior = vm.correo;
            vm.telefono_anterior = vm.telefono;
            vm.customer_name_anterior = vm.customer_name;

            evntBus.$emit('set_customer', records.name);


          } else {
            console.log("El nit del cliente existe en la base de datos PERO no ha sido validado por la SAT");

            let respuesta_verificar_nit = vm.verificacionNit(nitSinGuion);

            vm.visibleCamposComunes(true);
            vm.visible_btn_actualizar = true;
            vm.visible_btn_buscar = false;
            //vm.visibleCamposActualizar();
            if (respuesta_verificar_nit.nit_verificado !== 0) {
              vm.customer_name = respuesta_verificar_nit.nombre_comercial;
              vm.direccion = respuesta_verificar_nit.direccion;
              vm.customer_primary_key = records.name;
              vm.telefono = records.mobile_no;
              vm.correo = records.email_id;
              this.flag_nit_validado_con_exito = true;

              evntBus.$emit('set_customer', records.name);
              vm.mostrarAlerta = true;
              vm.displayError = "Nit verificado con exito, por favor actualice la informacion del cliente para continuar";
              vm.tipo_alerta = "success";
            } else {
              vm.mostrarAlerta = true;
              vm.displayError = "Nit no valido, verifique el nit e ingreselo nuevamente";
              vm.tipo_alerta = "error";
              vm.disable_nit_cliente = false;
              vm.visible_carga = false;


            }


          }

        } else {

          console.log("NO EXISTE EL CLIENTE");

          let respuesta_verificar_nit = vm.verificacionNit(nitSinGuion);
          if (respuesta_verificar_nit.nit_verificado !== 0) {
            vm.visibleCamposComunes(true);
            vm.visibleCamposNuevoCliente(true);
            vm.customer_name = respuesta_verificar_nit.nombre_comercial;
            vm.direccion = respuesta_verificar_nit.direccion;
            vm.mostrarAlerta = true;
            vm.displayError = "Cliente no existe, por favor ingrese los datos";
            vm.tipo_alerta = "info";
          } else {
            vm.mostrarAlerta = true;
            vm.displayError = "Nit no valido, verifique el nit e ingreselo nuevamente";
            vm.tipo_alerta = "error";
            vm.disable_nit_cliente = false;
            vm.visible_carga = false;
          }
          //ev.$emit('set_customer', records.name);


        }
      });


    },

    progressNewContact(progr) {
      frappe.hide_msgprint();
      if (progr === 100) {
        frappe.hide_progress('Creando');
      }
      else {
        frappe.show_progress('Creando', progr, 100, 'Creando Cliente ...');
      }

    },

    verificacionNit(nit_a_validar) {

      console.log('%c%s', 'color: green;', '--- INICIANDO VALIDACION NIT CLIENTE ---');

      let field = nit_a_validar;
      let field_no_chars;

      if (field.indexOf(' ') >= 0) {
        console.log("Valida si contiene espacio", field.length);
        field_no_chars = field.replace(/-|\s/g, "");
      } else {
        field_no_chars = nit_a_validar;
      }



      if (field_no_chars === 'CF' || field_no_chars === 'cf' || field_no_chars === 'Cf' || field_no_chars === 'cF') {
        frappe.hide_progress('Creando');
        return;
      }

      let datos = {
        nit_del_cliente: field_no_chars
      }

      let respuesta_verificar_nit;
      $.ajax({
        url: 'https://hook.integromat.com/mpn2pp4uh5656gqjdwgbi3j6ojaedudz',
        type: 'POST',
        data: datos,
        async: false,
        success: function (respuesta) {

          respuesta_verificar_nit = JSON.parse(respuesta);
          console.log('%c%s', 'color: magenta;', '- Respuesta de la validacion -', respuesta_verificar_nit);

          let address_from_nit = respuesta_verificar_nit.direccion;


          if (respuesta_verificar_nit.nit_verificado !== 0) {

            //Si la dirección esta vacio y no contiene letras se coloca CIUDAD
            if (!isEmpty(address_from_nit)) {
              if (address_from_nit.search(/([A-z])\w+/g) == -1) {
                console.log('NO CONTIENE LETRAS')
                respuesta_verificar_nit.direccion = 'CIUDAD'
              }
            }


            if (!isEmpty(respuesta_verificar_nit.nombre_comercial)) {

              //Se aplica la siguiente expresion regular para reemplazar las comas
              const regex = /\s*,\s*|\s+,/gm;
              const str = respuesta_verificar_nit.nombre_comercial;
              const subst = ` `;
              respuesta_verificar_nit.nombre_comercial = str.replace(regex, subst);
            }


          }



        }.bind(this),
        error: function () {
          frappe.throw(__('Error. No hay conexión para obtener la información. Espere unos minutos.'));
          console.log("No se ha podido obtener la información");
        }
      });

      return respuesta_verificar_nit;
    },




  },



  created: function () {
    evntBus.$on('open_validacion_nit', () => {
      this.customerDialog = true;
      this.nit_cliente = '';
      this.mostrarAlerta = false;
      this.disable_nit_cliente = false;
      this.limpiarCampos();
      this.visibleTodosCampos(false);
      this.visible_btn_buscar = true;
      this.visible_btn_cerrar = true;
      this.flag_nit_validado_con_exito = false;
      this.visible_carga = false;
      //COMENTARIO PRUEBA

    });
    evntBus.$on('register_pos_profile', (data) => {
      this.pos_profile = data.pos_profile;
    });

    this.getDepartamentos();
    this.getGrupoClientes();
    this.getReferencias();
    this.getClientesCF();

  },
};



function isEmpty(val) {
  var test = (val === undefined || val == null || val.length <= 0) ? true : false;
  return test;
}


function validateChangeFieldsEmailAndMobile(valueEmail, lastValueEmail, valueMobile, lastValueMobile) {
  let existChange = (valueEmail != lastValueEmail || valueMobile != lastValueMobile) ? true : false
  return existChange;
}

function replaceSpaceSlashHyphen(word) {
  word = word.replace(/\/|\s|\-/g, "");
  return word;
}

function getPlaceName(address, municipio) {

  let placeName;

  if (municipio == "Guatemala" && address != "Ciudad") {
    address = address.match(/[zZ][oO0][nN][aA]\s\d{0,25}/gm)[0];
    placeName = address.match(/\d+/)[0];
  } else if (municipio == "Guatemala" && address == "Ciudad") {
    placeName = "Zona 10"
  }
  else {
    placeName = municipio
  }

  return placeName;

}

function getZoneAddress(address) {

  if (address != "Ciudad") {
    address = address.match(/[zZ][oO0][nN][aA]\s\d{0,25}/gm)[0]
    address = address.match(/\d+/)[0];
  } else {
    address = "10"
  }

  return address;
}




</script>
