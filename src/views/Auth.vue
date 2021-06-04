<template>
	<div class="auth">
		<v-container>
			<v-row>
				<v-col cols=12>
					<v-card>
						<v-card-text>
							<v-text-field
					      label="Nomor Induk Mahasiswa"
					      placeholder="1100018001"
					      v-model="NIM"
					      type="number"
					    />
					    <v-text-field
					      label="Nama Lengkap Anda"
					      placeholder="Sherlock Holmes"
					      v-model="nama"
					    />

							<v-btn 
								elevation="2"
							  outlined
							  block
							  @click="submitData()"
							>
								LOGIN SEKARANG
							</v-btn>
						</v-card-text>
					</v-card>
				</v-col>
			</v-row>
		</v-container>
	</div>
</template>

<script>
	export default {
		name: 'Auth',
		data() {
			return {
				NIM: '',
				nama: ''
			}
		},
		created () {
			const getDataByLocalStorage = (data = []) => {
        return JSON.parse(data) || {};
    	}

    	const user = getDataByLocalStorage(localStorage.getItem('user'))

    	if(!user) this.$router.push('/');
		},
		methods: {
			submitData () {
				const user = {
					studentID: this.NIM,
					name: this.nama,
					authenticated: false
				}

				// simpan NIM dan nama ke dalam local storage
				localStorage.setItem('user', JSON.stringify(user));

				// larikan ke halaman presensi
				// this.$router.push('/');
				this.$router.go(-1);
			}
		}
	}
</script>