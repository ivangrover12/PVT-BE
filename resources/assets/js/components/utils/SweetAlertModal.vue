<script>
    export default {
      props:['docId', 'inboxState', 'docUserId','authId'],
      data(){
        return{
          status: this.inboxState,
          docUserIdLocal: this.docUserId
        }
      },
      methods: {
        confirmModal() {
          this.$swal({
            title: "¿Está seguro que revisó correctamente?",
            type: "warning",
            showCancelButton: true,
            confirmButtonColor: "#59B75C",
            cancelButtonColor: "#EC4758",
            confirmButtonText: "<i class='fa fa-save'></i> Confirmar",
            cancelButtonText: "Cancelar <i class='fa fa-times'></i>",
            showLoaderOnConfirm: true,
            preConfirm: () => {
                let uri = `/inbox_validate_doc/${this.docId}`
                return axios.patch(uri)
                .then(response => {
                    if (!response.data) {
                        throw new Error(response.errors)
                    }
                    return response.data;
                })
                .catch((error) => {
                    this.status = false;
                    this.$swal.showValidationError(
                      `Solicitud fallida: ${error.response.data.errors}`
                    )
                })
            },
            allowOutsideClick: () => !this.$swal.isLoading()
          }).then(result => {
            if (result.value) {
             this.status = true;
             this.docUserIdLocal = result.value.user_id;
             this.$swal({
               type: 'success',
               title: 'El Trámite fue procesado correctamente.',
               showConfirmButton: false,
               timer: 1500
             })
            }
          });
        },
        cancelModal() {
          this.$swal({
            title: "¿Está seguro de cancelar la revision del Tramite?",
            type: "error",
            showCancelButton: true,
            confirmButtonColor: "#59B75C",
            cancelButtonColor: "#EC4758",
            confirmButtonText: "<i class='fa fa-save'></i> Confirmar",
            cancelButtonText: "Cancelar <i class='fa fa-times'></i>",
            showLoaderOnConfirm: true,
            preConfirm: () => {
                let uri = `/inbox_invalidate_doc/${this.docId}`
                return axios.patch(uri)
                .then(response => {
                    if (!response.data) {
                        throw new Error(response.errors)
                    }
                    return response.data;
                })
                .catch((error) => {
                    this.status = true;
                    this.$swal.showValidationError(
                      `Solicitud fallida: ${error.response.data.errors}`
                    )
                })
            },
            allowOutsideClick: () => !this.$swal.isLoading()
          }).then(result => {
            if (result.value) {
             this.status = false;
             this.$swal({
               type: 'success',
               title: 'El Trámite fue procesado correctamente.',
               showConfirmButton: false,
               timer: 1500
             })
            }
          });
        },
      },
      computed:{
        itisMine(){
          return (this.docUserIdLocal == this.authId && this.status == true);
        }
      }
    };
</script>
