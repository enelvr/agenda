<template>
  <div class="container">
  	<div class="text-center mt-4">
  		<h3>Agenda de Contactos</h3>
  	</div>
  	<div class="row mt-4">
  		<div class="col-md-4">
  			<div class="card mt-4">
  				<div class="text-center mt-4">
  					Agregar contacto
  				</div>
  				<div class="card-body">
  					<form @submit.prevent="saveContacts">
  						<input type="text" class="form-control form-control-sm mb-2" placeholder="Nombres" v-model="name" required>
  						<input type="text" class="form-control form-control-sm mb-2" placeholder="Apellidos" v-model="lastname" required>
  						<input type="text" class="form-control form-control-sm mb-2" placeholder="teléfono" v-model="phone" required>
  						<input type="text" class="form-control form-control-sm mb-2" placeholder="Email" v-model="email" required>
  						<input type="text" class="form-control form-control-sm mb-2" placeholder="Dirección" v-model="address" required>
  						<button type="submit" class="btn btn-primary btn-sm float-right">{{ btn }}</button>
  					</form>
  				</div>
  			</div>

  		</div>
  		<div class="col-md-8">
  			<div class="card mt-4">
  				<div class="card-body">
  				<div class="mt-1 mb-4">Listado de Contactos</div>
  				<div class="search"><input type="text" placeholder="Buscar..." v-model="search" autocomplete="off"></div>
            <div class="table-responsive">
    					<table class="table table-hover table-sm mt-4">
    		  			<thead>
    		  				<tr>
    		  					<th>Nombres</th>
    		  					<th>Apellidos</th>
    		  					<th>Teléfono</th>
    		  					<th>Email</th>
    		  					<th>Acciones</th>
    		  				</tr>
    		  			</thead>
    		  			<tbody>
    		  				<tr v-for="(item, index) of searchContacts" :key="index">
    		  					<td>{{ item.name }}</td>
    		  					<td>{{ item.lastname }}</td>
    		  					<td>{{ item.phone }}</td>
    		  					<td>{{ item.email }}</td>
    		  					<td>
    		  						<button @click="verContact(item)" class="btn btn-sm btn-primary mr-1"><i class="fa fa-search"></i></button>
    		  						<button @click="editContact(index)" class="btn btn-sm btn-warning mr-1"><i class="fa fa-edit"></i></button>
    		  						<button @click="deleteContact(index)" class="btn btn-sm btn-danger"><i class="fa fa-trash"></i></button>
    		  					</td>
    		  				</tr>
    		  				<tr v-if="contacts.length === 0">
    		  					<td colspan="6" class="bg-primary text-white">No se encontraron contactos en la lista</td>
    		  				</tr>
    		  			</tbody>
    		  		</table>
            </div>
  				</div>
  			</div>
  		</div>
  	</div>
		</div>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
  	return {
  		search: null,
  		contacts: [],
  		id: null,
  		name: '',
  		lastname: '',
  		phone: '',
  		email: '',
  		address: '',
  		btn: 'Guardar',
  		edit: false,
  		showContact: false
   	}
  },

  methods: {
  	clear() {
  		this.name = '';
  		this.lastname = '';
  		this.phone = '';
  		this.email = '';
  		this.address = '';
  	},

  	saveContacts() {
  		if(this.edit === false) {
	  		this.contacts.push({
	  			name: this.name,
	  			lastname: this.lastname,
	  			phone: this.phone,
	  			email: this.email,
	  			address: this.address,
	  		});
	  		this.$swal.fire({
				text:'Contacto creado exitosamente',
				icon: 'success',
			})
  	} else {
  		let index = this.id;
  		this.contacts[index].name = this.name;
  		this.contacts[index].lastname = this.lastname;
  		this.contacts[index].phone = this.phone;
  		this.contacts[index].email = this.email;
  		this.contacts[index].address = this.address;
  		this.id = null;
  		this.edit = false;
  		this.btn = 'Guardar';
  		this.$swal.fire({
				text:'Contacto actualizado con éxito',
				icon: 'success',
			})

  	}
  		localStorage.setItem('agenda', JSON.stringify(this.contacts));
  		this.clear();
  	},

  	verContact(item) {
  		this.$swal.fire({
  			title: 'Datos de contacto',
  			html: '<b>Nombres:</b> ' + item.name + '<br>' +
  			'<b>Apellidos:</b> ' + item.lastname + '<br>' +
  			'<b>Teléfono:</b> ' + item.phone + '<br>' +
  			'<b>Email:</b> ' + item.email + '<br>' + 
  			'<hr>'+
  			'<b>dirección:</b> ' + item.address + '<br>',
  		})
  	},

  	editContact(index) {
  		this.id = index;
  		this.name = this.contacts[index].name;
  		this.lastname = this.contacts[index].lastname;
  		this.phone = this.contacts[index].phone;
  		this.email = this.contacts[index].email;
  		this.address = this.contacts[index].address;
  		this.edit = true;
  		this.btn = 'Editar';
  	},

  	deleteContact(index) {
  		this.$swal({
					title: 'Eliminar Contacto',
					text: 'Seguro que deseas eliminar este contacto?',
					icon: 'question',
					showCancelButton: true,
          confirmButtonText: "Eliminar",
          cancelButtonText: "Cancelar"
				}).then((result) => {
					if (result.isConfirmed) {
  					this.contacts.splice(index, 1);
  					localStorage.setItem('agenda', JSON.stringify(this.contacts));

						this.$swal.fire({
						  text:'Contacto Eliminado exitosamente',
						  icon: 'success',
						})
  				}
  			});
  	},
  },

  computed: {
  	searchContacts() {
  		if(this.search) {
  			return this.contacts.filter((item)=>{return this.search.toLowerCase().split(' ').every( v => item.name.toLowerCase().includes(v));
  			})
  		} else {
  			return this.contacts;
  		}
  	}
  },

  created: function() {
  	let baseDatos = JSON.parse(localStorage.getItem('agenda'));

  	if (baseDatos === null) {
  		this.contacts = [];
  	} else {
  		this.contacts = baseDatos;
  	}
  }
}
</script>
