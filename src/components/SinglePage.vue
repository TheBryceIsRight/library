<template>
  <div>
    <div class="container customPadding">
      <div class="customPadding">
        <div class="card card shadow-lg p-3 mb-5 bg-white rounded mr-5 ml-5">
          <div class="container">
            <div class="card-head" style="padding: 4rem">
              <h1 style="font-size: 6rem">{{ page.title }}</h1>

              <button
                type="button"
                class="btn btn-primary mr-2"
                data-toggle="modal"
                data-target="#exampleModal"
                data-whatever="@mdo"
              >
                Edit
              </button>

              <button
                type="button"
                class="btn btn-danger"
                @click="deletePost()"
              >
                Delete
              </button>
            </div>
            <hr />
            <p style="line-height: 26pt">
              {{ page.description }}
            </p>
          </div>
        </div>
      </div>
    </div>

    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">Edit Post</h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <form>
              <div class="form-group">
                <label for="recipient-name" class="col-form-label"
                  >title:</label
                >
                <input type="text" v-model="page.title" class="form-control" />
              </div>
              <div class="form-group">
                <label for="message-text" class="col-form-label">description:</label>
                <textarea class="form-control" v-model="page.description"></textarea>
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-primary" @click="editPot()">
              Save
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import gql from "graphql-tag";

export default {
  name: "SinglePage",
  data() {
    return {
      page: [],
      id: this.$route.params.id,
    };
  },

  methods: {
    editPot() {
      this.$apollo
        .mutate({
          mutation: gql`
            mutation updatePage($id: ID!, $Title: String!, $Body: String!) {
              updatePage(
                input: {
                  where: { id: $id }
                  data: { title: $title, description: $description }
                }
              ) {
                page {
                  title
                  description
                }
              }
            }
          `,
          variables: {
            id: this.id,
            title: this.page.title,
            description: this.page.description,
          },
        })
        .then((res) => {
          console.log(res);
          alert("Edited");
        });
    },

    deletePost() {
      const check = confirm();
      if (check) {
        this.$apollo
          .mutate({
            mutation: gql`
              mutation deletePage($id: ID!) {
                deletePage(input: { where: { id: $id } }) {
                  page {
                    title
                    description
                  }
                }
              }
            `,
            variables: {
              id: this.id,
            },
          })
          .then((res) => {
            console.log(res);
          });
      } else {
        return false;
      }
    },
  },

  apollo: {
    page: {
      query: gql`
        query page($id: ID!) {
          page(id: $id) {
            id
            title
            description
          }
        }
      `,
      variables() {
        return {
          id: this.id,
        };
      },
    },
  },
};
</script>


<style>
.customPadding {
  margin-top: 12rem !important;
  margin-bottom: 12rem !important;
  /* padding: 4rem; */
}
</style>