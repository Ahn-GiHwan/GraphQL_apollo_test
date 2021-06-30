# GraphQL Test

> apollo graphql을 이용하여 데이터 CRUD가 가능한 서버 구축

## 내용

> typeDef와 resolver를 통해 Schema를 정의하고 정의에 맞게 CRUD구현

```javascript
// Schema
const typeDefs = gql`
  type Query {
    books: [Book]
    book(bookId: Int): Book
  }
  type Mutation {
    addBook(title: String, message: String, author: String, url: String): Book
    editBook(
      bookId: Int
      title: String
      message: String
      author: String
      url: String
    ): Book
    deleteBook(bookId: Int): Book
  }

  type Book {
    bookId: Int
    title: String
    message: String
    author: String
    url: String
  }
`;

const resolvers = {
  // Read
  Query: {
    books: () => {
      return JSON.parse(readFileSync(join(__dirname, "books.json")).toString());
    },
    book: (parent, args, context, info) => {
      const books = JSON.parse(
        readFileSync(join(__dirname, "books.json")).toString()
      );
      return books.find((book) => book.bookId === args.bookId);
    },
  },
  Mutation: {
    // Create
    addBook: (parent, args, context, info) => {
      const books = JSON.parse(
        readFileSync(join(__dirname, "books.json")).toString()
      );
      const maxId = Math.max(...books.map((book) => book.bookId));
      const newBook = { bookId: maxId + 1, ...args };
      writeFileSync(
        join(__dirname, "books.json"),
        JSON.stringify([...books, newBook])
      );
      return newBook;
    },
    // Update
    editBook: (parent, args, context, info) => {
      const books = JSON.parse(
        readFileSync(join(__dirname, "books.json")).toString()
      );

      const newBooks = books.map((book) => {
        if (book.bookId === args.bookId) {
          return args;
        } else {
          return book;
        }
      });
      writeFileSync(join(__dirname, "books.json"), JSON.stringify(newBooks));
      return args;
    },
    // Delete
    deleteBook: (parent, args, context, info) => {
      const books = JSON.parse(
        readFileSync(join(__dirname, "books.json")).toString()
      );

      const deleted = books.find((book) => book.bookId === args.bookId);

      const newBooks = books.filter((book) => book.bookId !== args.bookId);

      writeFileSync(join(__dirname, "books.json"), JSON.stringify(newBooks));
      return deleted;
    },
  },
};
```

_(폴더에 내부에 books.json파일을 생성하여 데이터 구성)_
