# LibraryManagementSystem
package com.library.model;

public class Book {
    private final String bookId;
    private String title;
    private String author;
    private String genre;
    private String availabilityStatus;

    public Book(String bookId, String title, String author, String genre, String availabilityStatus) {
        if (title.trim().isEmpty() || author.trim().isEmpty()) {
            throw new IllegalArgumentException("Title and Author cannot be empty.");
        }
        this.bookId = bookId;
        this.title = title;
        this.author = author;
        this.genre = genre;
        setAvailabilityStatus(availabilityStatus);
    }

    public String getBookId() { return bookId; }
    public String getTitle() { return title; }
    public String getAuthor() { return author; }
    public String getGenre() { return genre; }
    public String getAvailabilityStatus() { return availabilityStatus; }

    public void setTitle(String title) {
        if (!title.trim().isEmpty()) this.title = title;
    }

    public void setAuthor(String author) {
        if (!author.trim().isEmpty()) this.author = author;
    }

    public void setAvailabilityStatus(String status) {
        if (status.equalsIgnoreCase("Available") || status.equalsIgnoreCase("Checked Out")) {
            this.availabilityStatus = status;
        } else {
            throw new IllegalArgumentException("Invalid status! Use 'Available' or 'Checked Out'.");
        }
    }

    @Override
    public String toString() {
        return "Book ID: " + bookId + " | Title: " + title + " | Author: " + author + 
               " | Genre: " + genre + " | Status: " + availabilityStatus;
    }
}





//(Custom Exception)

package com.library.exception;

public class BookNotFoundException extends RuntimeException {
    public BookNotFoundException(String message) {
        super(message);
    }
}




//(Business Logic)



package com.library.service;

import com.library.model.Book;
import com.library.exception.BookNotFoundException;
import java.util.*;

public class LibraryService {
    private final List<Book> books = new ArrayList<>();

    public void addBook(String bookId, String title, String author, String genre, String availabilityStatus) {
        if (bookIdExists(bookId)) {
            throw new IllegalArgumentException("Book ID already exists! Enter a unique ID.");
        }
        books.add(new Book(bookId, title, author, genre, availabilityStatus));
    }

    public List<Book> getAllBooks() {
        return new ArrayList<>(books);
    }

    public Book searchBook(String query) {
        return books.stream()
                .filter(book -> book.getBookId().equalsIgnoreCase(query) || book.getTitle().equalsIgnoreCase(query))
                .findFirst()
                .orElseThrow(() -> new BookNotFoundException("Book not found."));
    }

    public void updateBook(String bookId, String newTitle, String newAuthor, String newStatus) {
        Book book = searchBook(bookId);
        book.setTitle(newTitle);
        book.setAuthor(newAuthor);
        book.setAvailabilityStatus(newStatus);
    }

    public void deleteBook(String bookId) {
        if (!books.removeIf(book -> book.getBookId().equalsIgnoreCase(bookId))) {
            throw new BookNotFoundException("Book ID not found!");
        }
    }

    private boolean bookIdExists(String bookId) {
        return books.stream().anyMatch(book -> book.getBookId().equalsIgnoreCase(bookId));
    }
}



//(User Interface)


package com.library.ui;

import com.library.service.LibraryService;
import com.library.exception.BookNotFoundException;
import java.util.Scanner;

public class LibraryApp {
    private static final LibraryService library = new LibraryService();
    private static final Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        while (true) {
            System.out.println("\nLibrary System Menu:");
            System.out.println("1. Add Book");
            System.out.println("2. View All Books");
            System.out.println("3. Search Book");
            System.out.println("4. Update Book");
            System.out.println("5. Delete Book");
            System.out.println("6. Exit");
            System.out.print("Choose an option: ");

            int choice = scanner.nextInt();
            scanner.nextLine();
            try {
                switch (choice) {
                    case 1 -> addBook();
                    case 2 -> library.getAllBooks().forEach(System.out::println);
                    case 3 -> searchBook();
                    case 4 -> updateBook();
                    case 5 -> deleteBook();
                    case 6 -> exitApp();
                    default -> System.out.println("Invalid option! Try again.");
                }
            } catch (Exception e) {
                System.out.println("Error: " + e.getMessage());
            }
        }
    }

    private static void addBook() {
        System.out.print("Enter Book ID: ");
        String id = scanner.nextLine();
        System.out.print("Enter Title: ");
        String title = scanner.nextLine();
        System.out.print("Enter Author: ");
        String author = scanner.nextLine();
        System.out.print("Enter Genre: ");
        String genre = scanner.nextLine();
        System.out.print("Enter Availability Status (Available/Checked Out): ");
        String status = scanner.nextLine();
        library.addBook(id, title, author, genre, status);
        System.out.println("Book added successfully!");
    }

    private static void searchBook() {
        System.out.print("Enter Book ID or Title: ");
        System.out.println(library.searchBook(scanner.nextLine()));
    }

    private static void updateBook() {
        System.out.print("Enter Book ID: ");
        String id = scanner.nextLine();
        System.out.print("Enter New Title: ");
        String title = scanner.nextLine();
        System.out.print("Enter New Author: ");
        String author = scanner.nextLine();
        System.out.print("Enter New Status: ");
        String status = scanner.nextLine();
        library.updateBook(id, title, author, status);
        System.out.println("Book updated!");
    }

    private static void deleteBook() {
        System.out.print("Enter Book ID: ");
        library.deleteBook(scanner.nextLine());
        System.out.println("Book deleted!");
    }

    private static void exitApp() {
        System.out.println("Exiting...");
        scanner.close();
        System.exit(0);
    }
}


