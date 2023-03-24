# Project Work Time - Dates

## Learning Goals

- Spend some time working on your project.

## Introduction

You can now work on Task 4 in the Module Project. Task 4 has been copied here
for your convenience.

## Task 4 - Dates

Now that we have learned about date and time objects, let's put them to use in
our project. Every `Concert` should have a date associated with it. When we add
this new instance variable, we'll have to make a few changes to our existing
concert application.

Modify the `Concert` class.

- Add an instance variable called `concertDate` of the appropriate date-time
   object.
  - Use a date-time object from the `Date and Time API`.
  - Hint: Look at the unit tests for a hint.
- Update the constructor to take in the `concertDate` as a parameter.
- Use IntelliJ to generate a getter method for `concertDate`.
- Update the `toString()` method to include the `concertDate`.

Modify the `ConcertService` class appropriately given the changes made to the
`Concert` class.

Consider the following changes to the `Driver`:

```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeParseException;
import java.time.format.ResolverStyle;
import java.util.InputMismatchException;
import java.util.Scanner;
   
public class Driver {
   
    private static ConcertService service = new ConcertService();
   
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String prompt = "Select an action: " +
                "a=add concert, " +
                "d=display all concerts, " +
                "p=purchase ticket, " +
                "w=add to waitlist," +
                " q=quit: ";
        String action;
   
        // Prompt the user for an action
       // Loop until the user enters q to quit
       do {
           System.out.println(prompt);
           action = scanner.nextLine();
   
           switch (action) {
               case "a":
                   addConcert();
                   break;
               case "d":
                   service.displayConcerts();
                   break;
               case "p":
                   System.out.println("Enter the performer's name:");
                   service.purchaseTicket( scanner.nextLine() );
                   break;
               case "w":
                   System.out.println("Enter the performer's name:");
                   service.addToWaitlist( scanner.nextLine() );
                   break;
               case "q":
                   break;
               default:
                   System.out.println("Invalid choice: " + action);
           }
       }
       while (!action.equals("q"));
    }
   
    private static void addConcert() {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Enter the performer's name:");
        String performer = scanner.nextLine();
        try {
            System.out.println("Enter the number of available tickets:");
            int available = scanner.nextInt();
            scanner.nextLine();    // Consume the rest of the input
   
           DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/uuuu")
                   .withResolverStyle(ResolverStyle.STRICT);
           System.out.println("Enter a date for the concert in the format of MM/dd/uuuu:");
           String concertDate = scanner.nextLine();
   
           // Validate the date
           LocalDate date = LocalDate.parse(concertDate, formatter);
           service.addConcert(performer, available, date);
        }
        catch (InputMismatchException e) {
            System.out.println("Number of available tickets was not an integer");
        } catch (DateTimeParseException e) {
            System.out.println("Concert date was not a valid date");
        }
    }
}
```

Take note of how we are prompting the user for a concert date and how we are
validating the date is valid.

If we run the driver class now and add a concert, an expected output could be
this:

```text
Select an action: a=add concert, d=display all concerts, p=purchase ticket, w=add to waitlist, q=quit: 
a
Enter the performer's name:
Taylor Swift
Enter the number of available tickets:
500
Enter a date for the concert in the format of MM/dd/uuuu:
03/17/2023
Added concert
Select an action: a=add concert, d=display all concerts, p=purchase ticket, w=add to waitlist, q=quit: 
a
Enter the performer's name:
Harry Styles
Enter the number of available tickets:
750
Enter a date for the concert in the format of MM/dd/uuuu:
02/29/2023
Concert date was not a valid date
Select an action: a=add concert, d=display all concerts, p=purchase ticket, w=add to waitlist, q=quit: 
q
```

Modify the following unit tests below and ensure the tests all pass.

Update the `ConcertTest`:

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.time.LocalDate;

import static org.junit.jupiter.api.Assertions.*;

class ConcertTest {

    private Concert c1, c2;

    @BeforeEach
    void setup() {
         c1 = new Concert("The Weeknd", 10, LocalDate.of(2023, 3, 15));
         c2 = new Concert("Harry Styles", 2, LocalDate.of(2023, 3, 17));
    }

    @Test
    void constructor() {
        assertEquals("The Weeknd", c1.getPerformer());
        assertEquals(10, c1.getAvailable());
        assertEquals(0, c1.getWaitlist());
       assertEquals(LocalDate.of(2023, 3, 15), c1.getConcertDate());

        assertEquals("Harry Styles", c2.getPerformer());
        assertEquals(2, c2.getAvailable());
        assertEquals(0, c2.getWaitlist());
       assertEquals(LocalDate.of(2023, 3, 17), c2.getConcertDate());
    }

    @Test
    void testToString() {
        assertEquals("Concert{performer='The Weeknd', available=10, waitlist=0, concertDate=2023-03-15}", c1.toString());
        assertEquals("Concert{performer='Harry Styles', available=2, waitlist=0, concertDate=2023-03-17}", c2.toString());
    }

    @Test
    void purchaseTicket() {
        assertEquals(10, c1.getAvailable());
        assertTrue(c1.purchaseTicket());
        assertEquals(9, c1.getAvailable());
        assertTrue(c1.purchaseTicket());
        assertEquals(8, c1.getAvailable());

        assertEquals(2, c2.getAvailable());
        assertTrue(c2.purchaseTicket());
        assertEquals(1, c2.getAvailable());
        assertTrue(c2.purchaseTicket());
        assertEquals(0, c2.getAvailable());
        //no tickets left, remain at 0
        assertFalse(c2.purchaseTicket());
        assertEquals(0, c2.getAvailable());
    }

    @Test
    void addToWaitlist() {
        assertEquals(0, c1.getWaitlist());
        c1.addToWaitlist();
        assertEquals(1, c1.getWaitlist());
        c1.addToWaitlist();
        assertEquals(2, c1.getWaitlist());

        assertEquals(0, c2.getWaitlist());
        c2.addToWaitlist();
        assertEquals(1, c2.getWaitlist());
        c2.addToWaitlist();
        assertEquals(2, c2.getWaitlist());
    }
}
```

Update the `ConcertRepositoryTest`:

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.time.LocalDate;

import static org.junit.jupiter.api.Assertions.*;

class ConcertRepositoryTest {

    private ConcertRepository repository;

    @BeforeEach
    void setUp() {
        repository = new ConcertRepository();
    }

    @Test
    void constructor() {

        // current size is 0 since no concerts have been added
        assertEquals(0, repository.getAllConcerts().size());
    }

    @Test
    void addGet1() {
        assertEquals(0, repository.getAllConcerts().size());

        // add a concert
        assertTrue(repository.add(new Concert("Artist0", 1000, LocalDate.of(2023, 3, 15))));
        assertEquals(1, repository.getAllConcerts().size());

        // retrieve the concert using index 0
        Concert c = repository.get(0);
        assertEquals("Artist0", c.getPerformer());
        assertEquals(1000, c.getAvailable());

    }

    @Test
    void addGet5() {
        assertEquals(0, repository.getAllConcerts().size());

        // add 5 concerts
        for (int i = 0; i < 5; i++) {
            // add the concert
            assertTrue(repository.add(new Concert("Artist" + i, i * 1000, LocalDate.of(2023, 3, i+1))));

            // retrieve the concert using index i
            assertNotNull(repository.get(i));

            // confirm the current size
            assertEquals(i + 1, repository.getAllConcerts().size());
        }

        // confirm the size did not increase
        assertEquals(5, repository.getAllConcerts().size());
    }

    @Test
    void getConcertState() {
        // add 3 concerts
        assertTrue(repository.add(new Concert("The Weeknd", 1000, LocalDate.of(2023, 3, 15))));
        assertTrue(repository.add(new Concert("Taylor Swift", 500, LocalDate.of(2023, 3, 16))));
        assertTrue(repository.add(new Concert("Harry Styles", 20000, LocalDate.of(2023, 3, 17))));
        assertEquals(3, repository.getAllConcerts().size());

        // confirm each concert was inserted in the correct array position
        assertEquals("The Weeknd", repository.get(0).getPerformer());
        assertEquals("Taylor Swift", repository.get(1).getPerformer());
        assertEquals("Harry Styles", repository.get(2).getPerformer());
    }

    @Test
    public void getOutOfBounds() {
        assertTrue(repository.add(new Concert("artist1", 1000, LocalDate.of(2023, 3, 15))));
        assertTrue(repository.add(new Concert("artist2", 1000, LocalDate.of(2023, 3, 16))));
        assertTrue(repository.add(new Concert("artist3", 1000, LocalDate.of(2023, 3, 17))));

        // test that out of bounds index returns null
        assertNull(repository.get(-1));
        assertNull(repository.get(3));
    }


    @Test
    void findByPerformer() {
        repository.add(new Concert("Taylor Swift", 1000, LocalDate.of(2023, 3, 15)));
        repository.add(new Concert("The Weeknd", 500, LocalDate.of(2023, 3, 17)));

        Concert c1 = repository.findByPerformer("Taylor Swift");
        assertEquals("Taylor Swift", c1.getPerformer());
        assertEquals(1000, c1.getAvailable());
        assertEquals(0, c1.getWaitlist());

        Concert c2 = repository.findByPerformer("The Weeknd");
        assertEquals("The Weeknd", c2.getPerformer());
        assertEquals(500, c2.getAvailable());
        assertEquals(0, c2.getWaitlist());

        //unknown performer
        Concert c3 = repository.findByPerformer("Unknown Singer");
        assertNull(c3);

    }

    @Test
    void caseInsensitiveFind() {
        repository.add(new Concert("Taylor Swift", 1000, LocalDate.of(2023, 3, 15)));
        repository.add(new Concert("The Weeknd", 500, LocalDate.of(2023, 3, 16)));

        Concert c1 = repository.findByPerformer("TAYLOR swift");
        assertEquals("Taylor Swift", c1.getPerformer());
        assertEquals(1000, c1.getAvailable());
        assertEquals(0, c1.getWaitlist());

    }
}
```

Update the `ConcertServiceTest`:

```java
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.io.ByteArrayOutputStream;
import java.io.PrintStream;
import java.time.LocalDate;
import java.util.Arrays;

import static org.junit.jupiter.api.Assertions.*;

class ConcertServiceTest {

    private final PrintStream standardOut = System.out;
    private final ByteArrayOutputStream outputStreamCaptor = new ByteArrayOutputStream();
    private ConcertService concertService;

    @BeforeEach
    public void setUp() {
        System.setOut(new PrintStream(outputStreamCaptor));
        concertService = new ConcertService();
    }

    @AfterEach
    public void tearDown() {
        System.setOut(standardOut);
    }

    @Test
    void duplicateAdd() {
        concertService.addConcert("Taylor Swift" , 100, LocalDate.of(2023, 3, 15));
        concertService.addConcert("Taylor Swift" , 200, LocalDate.of(2023, 3, 15));
        assertEquals("Added concert\n" +
                        "Concert with Taylor Swift already exists. Unable to add concert",
                outputStreamCaptor.toString().trim());
    }

    @Test
    void displayEmpty() {
        concertService.displayConcerts();
        assertEquals("", outputStreamCaptor.toString().trim());
    }

    @Test
    void displayNonEmpty() {
        concertService.addConcert("Taylor Swift" , 100, LocalDate.of(2023, 3, 15));
        concertService.addConcert("The Weeknd", 5000, LocalDate.of(2023, 3, 16));
        concertService.displayConcerts();
        assertEquals("Added concert\n" +
                        "Added concert\n" +
                        "Concert{performer='Taylor Swift', available=100, waitlist=0, concertDate=2023-03-15}\n" +
                        "Concert{performer='The Weeknd', available=5000, waitlist=0, concertDate=2023-03-16}",
                outputStreamCaptor.toString().trim());
    }



    @Test
    void purchaseTicket() {
        concertService.addConcert("Taylor Swift" , 3, LocalDate.of(2023, 3, 15));
        concertService.purchaseTicket("Taylor Swift");
        concertService.purchaseTicket("Taylor Swift");
        concertService.purchaseTicket("Taylor Swift");
        // sold out, ticket unavailable
        concertService.purchaseTicket("Taylor Swift");
        assertEquals("Added concert\n" +
                        "Ticket purchased\n" +
                        "Ticket purchased\n" +
                        "Ticket purchased\n" +
                        "Ticket unavailable",
                outputStreamCaptor.toString().trim());
    }

    @Test
    void purchaseTicketUnknownArtist() {
        concertService.addConcert("Taylor Swift" , 1000, LocalDate.of(2023, 3, 15));
        concertService.purchaseTicket("Unknown Singer");
        assertEquals("Added concert\n" +
                        "No concert for Unknown Singer",
                outputStreamCaptor.toString().trim());
    }

    @Test
    void addToWaitlist() {
        concertService.addConcert("Taylor Swift" , 100, LocalDate.of(2023, 3, 15));
        concertService.addConcert("The Weeknd", 5000, LocalDate.of(2023, 3, 15));
        concertService.addToWaitlist("Taylor Swift");
        concertService.addToWaitlist("Taylor Swift");
        concertService.addToWaitlist("The Weeknd");
        // no concert
        concertService.addToWaitlist("Unknown Singer");

        assertEquals("Added concert\n" +
                        "Added concert\n" +
                        "Added to waitlist\n" +
                        "Added to waitlist\n" +
                        "Added to waitlist\n" +
                        "No concert for Unknown Singer",
                outputStreamCaptor.toString().trim());
    }
}
```

This task can be completed now.

## Resources

- [Java 17 LocalDate](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/time/LocalDate.html)
