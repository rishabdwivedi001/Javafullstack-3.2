import React, { useMemo, useState } from "react";
import "./index.css"; // make sure this line exists

export default function App() {
  const [books, setBooks] = useState([
    { id: 1, title: "1984", author: "George Orwell" },
    { id: 2, title: "The Great Gatsby", author: "F. Scott Fitzgerald" },
    { id: 3, title: "To Kill a Mockingbird", author: "Harper Lee" },
  ]);

  const [query, setQuery] = useState("");
  const [newTitle, setNewTitle] = useState("");
  const [newAuthor, setNewAuthor] = useState("");

  const visible = useMemo(() => {
    const q = query.trim().toLowerCase();
    return q
      ? books.filter(
          (b) =>
            b.title.toLowerCase().includes(q) ||
            b.author.toLowerCase().includes(q)
        )
      : books;
  }, [books, query]); // filtering lists [web:64]

  function addBook(e) {
    e.preventDefault(); // stop page reload on submit [web:74]
    const title = newTitle.trim();
    const author = newAuthor.trim();
    if (!title || !author) return;
    const nextId = books.length ? Math.max(...books.map((b) => b.id)) + 1 : 1;
    setBooks((prev) => [...prev, { id: nextId, title, author }]); // immutable update [web:52]
    setNewTitle("");
    setNewAuthor("");
  }

  function removeBook(id) {
    setBooks((prev) => prev.filter((b) => b.id !== id)); // immutable remove [web:52]
  }

  return (
    <div className="page">
      {/* Panel 1: Interactive */}
      <section className="panel">
        <h2 className="heading">Library Management</h2>

        <input
          className="search"
          placeholder="Search by title or author"
          value={query}
          onChange={(e) => setQuery(e.target.value)}
        />

        <form onSubmit={addBook} className="formRow">
          <input
            className="shortInput"
            placeholder="New book title"
            value={newTitle}
            onChange={(e) => setNewTitle(e.target.value)}
          />
          <input
            className="shortInput"
            placeholder="New book author"
            value={newAuthor}
            onChange={(e) => setNewAuthor(e.target.value)}
          />
          <button type="submit" className="addBtn">Add Book</button>
        </form>

        <div className="list">
          {visible.map((b) => (
            <div key={b.id} className="item">
              <div className="text">
                <strong>{b.title}</strong> by {b.author}
              </div>
              <button className="removeBtn" onClick={() => removeBook(b.id)}>
                Remove
              </button>
            </div>
          ))}
        </div>
      </section>

      {/* Panel 2: Static preview filtered to “great” */}
      <section className="panel">
        <h2 className="heading">Library Management</h2>

        <input className="search" value="great" readOnly />

        <form onSubmit={(e) => e.preventDefault()} className="formRow">
          <input className="shortInput" placeholder="New book title" />
          <input className="shortInput" placeholder="New book author" />
          <button className="addBtn" disabled>Add Book</button>
        </form>

        <div className="list">
          {books
            .filter((b) => b.title.toLowerCase().includes("great"))
            .map((b) => (
              <div key={`static-${b.id}`} className="item">
                <div className="text">
                  <strong>{b.title}</strong> by {b.author}
                </div>
                <button className="removeBtn">Remove</button>
              </div>
            ))}
        </div>
      </section>
    </div>
  );
}
