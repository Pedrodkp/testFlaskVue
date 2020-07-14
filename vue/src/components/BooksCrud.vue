<template>
    <div class="container">
        <div class="row">
            <div class="col-sm-10">
                <h1>Books</h1>
                <hr><br><br>
                <alertcrud :message="message" v-if="showMessage"></alertcrud>
                <button type="button" class="btn btn-success btn-sm"
                        v-b-modal.book-modal
                        @click="addBook()">Add Book</button>
                <br><br>
                <table class="table table-hover">
                    <thead>
                        <tr>
                            <th scope="col">Title</th>
                            <th scope="col">Author</th>
                            <th scope="col">Read?</th>
                            <th></th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(book, index) in books" :key="index">
                            <td>{{ book.title }}</td>
                            <td>{{ book.author }}</td>
                            <td>
                                <span v-if="book.read">Yes</span>
                                <span v-else>No</span>
                            </td>
                            <td>
                                <div class="btn-group" role="group">
                                    <button type="button"
                                            class="btn btn-warning btn-sm"
                                            v-b-modal.book-modal
                                            @click="editBook(book)">
                                        Update
                                    </button>
                                    <button type="button"
                                            class="btn btn-danger btn-sm"
                                            @click="onDeleteBook(book)">
                                        Delete
                                    </button>
                                </div>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

        <b-modal ref="bookModal"
                 id="book-modal"
                 :title=this.modalTitle
                 hide-footer>
          <b-form @submit="onSubmit" @reset="onReset" class="w-100">
            <b-form-group id="form-title-group"
                          label="Title:"
                          label-for="form-title-input">
              <b-form-input id="form-title-input"
                            type="text"
                            v-model="bookForm.title"
                            required
                            placeHolder="Enter title">
              </b-form-input>
            </b-form-group>
            <b-form-group id="form-author-group"
                          label="Author:"
                          label-for="form-author-input">
              <b-form-input id="form-author-input"
                            type="text"
                            v-model="bookForm.author"
                            required
                            placeHolder="Enter author">
              </b-form-input>
            </b-form-group>
            <b-form-group id="form-read-group">
              <b-form-checkbox-group v-model="bookForm.read" id="form-checks">
                <b-form-checkbox value="true">Read?</b-form-checkbox>
              </b-form-checkbox-group>
            </b-form-group>
            <b-button-group>
              <b-button type="submit" variant="primary">Submit</b-button>
              <b-button type="reset" variant="danger">Cancel</b-button>
            </b-button-group>
          </b-form>
        </b-modal>

    </div>
</template>

<script>
import axios from 'axios';
import AlertCrud from './AlertCrud.vue';

export default {
  name: 'BooksCrud',
  data() {
    return {
      books: [],
      bookForm: {
        id: '',
        title: '',
        author: '',
        read: [],
      },
      message: '',
      modalTitle: '',
      showMessage: false,
    };
  },
  components: {
    alertcrud: AlertCrud,
  },
  methods: {
    getBooks() {
      const path = 'http://localhost:5000/books';
      axios.get(path)
        .then((res) => {
          this.books = res.data.books;
        })
        .catch((error) => {
          console.error(error);
        });
    },
    book(payload) {
      const path = 'http://localhost:5000/books';
      axios.post(path, payload)
        .then(() => {
          this.getBooks();
          this.message = 'Book added!';
          this.showMessage = true;
        })
        .catch((error) => {
          console.log(error);
          this.getBooks();
        });
    },
    updateBook(payload, bookID) {
      const path = `http://localhost:5000/books/${bookID}'`;
      axios.put(path, payload)
        .then(() => {
          this.getBooks();
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getBooks();
        });
    },
    removeBook(bookID) {
      const path = `http://localhost:5000/books/${bookID}`;
      axios.delete(path)
        .then(() => {
          this.getBooks();
          this.message = 'Book removed!';
          this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getBooks();
        });
    },
    initForm() {
      this.bookForm.id = '';
      this.bookForm.title = '';
      this.bookForm.author = '';
      this.bookForm.read = [];
    },
    addBook() {
      this.modalTitle = 'Add new book';
    },
    editBook(book) {
      this.bookForm = book;
      this.modalTitle = `Edit the book ${book.id}`;
    },
    onSubmit(evt) {
      evt.preventDefault();
      this.$refs.bookModal.hide();
      let read = false;
      if (this.bookForm.read[0]) read = true;
      const payload = {
        title: this.bookForm.title,
        author: this.bookForm.author,
        read,
      };
      if (this.bookForm.id) {
        this.updateBook(payload, this.editForm.id);
      } else {
        this.book(payload);
      }
      this.initForm();
    },
    onReset(evt) {
      evt.preventDefault();
      this.$refs.bookModal.hide();
      this.initForm();
      this.getBooks();
    },
    onDeleteBook(book) {
      // eslint-disable-next-line
      if (confirm('Do you really want to delete?')) {
        this.removeBook(book.id);
      }
    },
  },
  created() {
    this.getBooks();
  },
};
</script>
