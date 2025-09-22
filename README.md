import React, { useMemo, useState } from "react";

export default function App() {
  const [books, setBooks] = useState([
    { id: 1, title: "1984", author: "George Orwell" },
    { id: 2, title: "The Great Gatsby", author: "F. Scott Fitzgerald" },
    { id: 3, title: "To Kill a Mockingbird", author: "Harper Lee" },
  ]);
  const [query, setQuery] = useState("");
  const [title, setTitle] = useState("");
  const [author, setAuthor] = useState("");

  const filtered = useMemo(() => {
    const q = query.trim().toLowerCase();
    if (!q) return books;
    return books.filter(
      (b) =>
        b.title.toLowerCase().includes(q) ||
        b.author.toLowerCase().includes(q)
    );
  }, [books, query]); // real-time filtering [web:64]

  function addBook(e) {
    e.preventDefault();
    const t = title.trim();
    const a = author.trim();
    if (!t || !a) return;
    const nextId = books.length ? Math.max(...books.map((b) => b.id)) + 1 : 1;
    setBooks([...books, { id: nextId, title: t, author: a }]); // immutable add [web:52]
    setTitle("");
    setAuthor("");
  }

  function removeBook(id) {
    setBooks(books.filter((b) => b.id !== id)); // immutable remove [web:52]
  }

  return (
    <div style={{ display: "grid", gap: 18 }}>
      {/* Top panel */}
      <Panel>
        <Heading>Library Management</Heading>

        <input
          placeholder="Search by title or author"
          value={query}
          onChange={(e) => setQuery(e.target.value)}
          style={styles.search}
        />

        <form onSubmit={addBook} style={styles.formRow}>
          <input
            placeholder="New book title"
            value={title}
            onChange={(e) => setTitle(e.target.value)}
            style={styles.shortInput}
          />
          <input
            placeholder="New book author"
            value={author}
            onChange={(e) => setAuthor(e.target.value)}
            style={styles.shortInput}
          />
          <button type="submit" style={styles.addBtn}>Add Book</button>
        </form>

        <List>
          {books.map((b) => (
            <ListItem key={b.id}>
              <div>
                <strong>{b.title}</strong> by {b.author} {/* keys + bold title [web:66] */}
              </div>
              <button style={styles.removeBtn} onClick={() => removeBook(b.id)}>
                Remove
              </button>
            </ListItem>
          ))}
        </List>
      </Panel>

      {/* Bottom panel shows filtered state "great" */}
      <Panel>
        <Heading>Library Management</Heading>

        <input
          value="great"
          readOnly
          style={styles.search}
        />

        <form onSubmit={(e) => e.preventDefault()} style={styles.formRow}>
          <input placeholder="New book title" style={styles.shortInput} />
          <input placeholder="New book author" style={styles.shortInput} />
          <button style={styles.addBtn} disabled>Add Book</button>
        </form>

        <List>
          {filtered
            .filter((b) =>
              b.title.toLowerCase().includes("great")
            )
            .map((b) => (
              <ListItem key={`preview-${b.id}`}>
                <div>
                  <strong>{b.title}</strong> by {b.author}
                </div>
                <button style={styles.removeBtn}>Remove</button>
              </ListItem>
            ))}
        </List>
      </Panel>
    </div>
  );
}

function Panel({ children }) {
  return (
    <div style={styles.panel}>
      {children}
    </div>
  );
}

function Heading({ children }) {
  return <h2 style={styles.heading}>{children}</h2>;
}

function List({ children }) {
  return <div style={{ display: "grid", gap: 10 }}>{children}</div>;
}

function ListItem({ children }) {
  return (
    <div style={styles.item}>
      {children}
    </div>
  );
}

const styles = {
  panel: {
    border: "2px solid #222",
    padding: 12,
    borderRadius: 2,
    background: "#fff",
  },
  heading: { margin: "4px 0 12px 0", fontSize: 26, fontWeight: 700 },
  search: {
    width: 320,
    padding: 8,
    border: "1px solid #cfcfcf",
    borderRadius: 2,
    marginBottom: 12,
  },
  formRow: {
    display: "flex",
    gap: 12,
    alignItems: "center",
    marginBottom: 12,
  },
  shortInput: {
    width: 220,
    padding: 8,
    border: "1px solid #cfcfcf",
    borderRadius: 2,
  },
  addBtn: {
    padding: "8px 12px",
    border: "1px solid #bfbfbf",
    borderRadius: 2,
    background: "#eee",
    cursor: "pointer",
  },
  item: {
    display: "flex",
    justifyContent: "space-between",
    alignItems: "center",
    border: "1px solid #ddd",
    borderRadius: 6,
    padding: "10px 12px",
    background: "#fff",
  },
  removeBtn: {
    padding: "6px 10px",
    border: "1px solid #bfbfbf",
    borderRadius: 4,
    background: "#eee",
    cursor: "pointer",
  },
};
