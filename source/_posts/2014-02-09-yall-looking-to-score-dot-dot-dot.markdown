---
layout: post
title: "Y'all looking to score..."
date: 2014-02-09 22:02:43 -0600
comments: true
categories:
---
"Y'all looking to score some crack?"

That's what was proposed to me and a member of my cohort at the bus stop yesterday. An interesting start to an otherwise awesome weekend.
<!-- more -->

We were going at it pretty hard all week and Saturday. The exercises in class were challenging, but many of the cohort still have yet to finish Friday's assignment, which involved using rSpec - the tests were pre-written - to build a Library application in Ruby.

I was one of maybe a handful that finished the project on Friday afternoon, but I don't think it necessarily indicates that I am further along than most ... I just happened to finish. I already received the review from one of our instructors, and he said that I did a decent job, but there are some flaws in my logic that can be shored up.

Here were the requirements for the Library app:

- A Book should be able to be marked as checked_out
- You should be able to check a Book's status (e.g. available or checked out)
- You should be able to add new Books to a Library
- A Borrower should be able to check out a Book
- Checked-out Books should be associated with a Borrower
- Borrowers should be able to check Books back in to the Library (when they're finished with them)
- A Borrower should not be able to check out more than two Books at any given time
- The Library should be able to list available books and borrowed books

Here's some of the code:

```ruby
class Book
  attr_reader :author, :title, :id, :status

  def initialize(title = nil, author = nil, id = nil)
    @author = author
    @title = title
    @id = id
    @status = 'available'
  end

  def check_out
    if @status == 'checked_out'
      false
    else
      @status = 'checked_out'
      true
    end
  end

  def check_in
    @status = 'available'
  end
end

class Borrower
  attr_accessor :name, :count

  def initialize(name)
    @name = name
    @count = 0
  end

end

class Library
  attr_accessor :available_books, :borrowed_books
  attr_reader :books
  def initialize(name = nil)
    @books = []
    @available_books = []
    @borrowed_books = {}
  end

  def register_new_book(title, author)
    @books.push(Book.new(title, author, books.count))
    @available_books.push(Book.new(title, author, books.count))
  end

  def check_out_book(book_id, borrower)
    borrower.count += 1
    if borrower.count > 2
      nil
    else
      if @books[book_id].status == 'available'
        @books[book_id].check_out
        @borrower = borrower
        @gone = @available_books.pop(book_id)
        # @borrowed_books.push @gone
        @books[book_id]
      end
    end
  end

  def check_in_book(book)
    book.check_in
  end

  def get_borrower(book)
    @borrower.name
  end
end
```

I used an array to hold the books, and used the array index as the book id ..... lame. If the Library decided to remove some books, everything would shift down and the id's would change. A hash would have been a better solution and I could use something like <code>@books.find {|book| book.id == book_id}</code> to access the id. I could have used a counter for the book's id and incremented the counter everytime a book was added. Oh well, next time!

I'm looking forward to this coming week.
