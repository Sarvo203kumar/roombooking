import java.util.*;

public class SmartOfficeApp {
    public static void main(String[] args) {
        Office office = Office.getInstance();
        CommandExecutor executor = new CommandExecutor();
        
        Scanner scanner = new Scanner(System.in);
        while (true) {
            System.out.println("Enter command:");
            String commandLine = scanner.nextLine();
            executor.executeCommand(commandLine, office);
        }
    }
}

public class Office {
    private static Office instance;
    private Map<Integer, Room> rooms = new HashMap<>();
    
    private Office() {}
    
    public static Office getInstance() {
        if (instance == null) {
            instance = new Office();
        }
        return instance;
    }
    
    public void configureRooms(int numberOfRooms) {
        rooms.clear();
        for (int i = 1; i <= numberOfRooms; i++) {
            rooms.put(i, new Room(i));
        }
        System.out.println("Office configured with " + numberOfRooms + " meeting rooms.");
    }
    
    public Room getRoom(int roomNumber) {
        return rooms.get(roomNumber);
    }
    
    public Collection<Room> getAllRooms() {
        return rooms.values();
    }
}

public class Room {
    private int roomNumber;
    private int maxCapacity;
    private boolean isBooked;
    private boolean isOccupied;
    private int occupants;
    private Timer timer;
    
    public Room(int roomNumber) {
        this.roomNumber = roomNumber;
        this.isBooked = false;
        this.isOccupied = false;
        this.occupants = 0;
        this.timer = new Timer();
    }
    
    public int getRoomNumber() {
        return roomNumber;
    }
    
    public void setMaxCapacity(int maxCapacity) {
        this.maxCapacity = maxCapacity;
        System.out.println("Room " + roomNumber + " maximum capacity set to " + maxCapacity + ".");
    }
    
    public void addOccupant(int numberOfOccupants) {
        this.occupants = numberOfOccupants;
        if (this.occupants >= 2) {
            this.isOccupied = true;
            System.out.println("Room " + roomNumber + " is now occupied by " + occupants + " persons. AC and lights turned on.");
        } else {
            this.isOccupied = false;
            System.out.println("Room " + roomNumber + " is now unoccupied. AC and lights turned off.");
        }
    }
    
    public boolean isBooked() {
        return isBooked;
    }
    
    public void bookRoom(String time, int duration) {
        if (isBooked) {
            System.out.println("Room " + roomNumber + " is already booked during this time. Cannot book.");
        } else {
            isBooked = true;
            timer.schedule(new TimerTask() {
                @Override
                public void run() {
                    if (!isOccupied) {
                        isBooked = false;
                        System.out.println("Room " + roomNumber + " booking released automatically. AC and lights turned off.");
                    }
                }
            }, duration * 60 * 1000);
            System.out.println("Room " + roomNumber + " booked from " + time + " for " + duration + " minutes.");
        }
    }
    
    public void cancelBooking() {
        if (isBooked) {
            isBooked = false;
            timer.cancel();
            System.out.println("Booking for Room " + roomNumber + " cancelled successfully.");
        } else {
            System.out.println("Room " + roomNumber + " is not booked. Cannot cancel booking.");
        }
    }
}

public class CommandExecutor {
    public void executeCommand(String commandLine, Office office) {
        String[] commandParts = commandLine.split(" ");
        String command = commandParts[0];
        
        switch (command) {
            case "config":
                if (commandParts[1].equals("room") && commandParts[2].equals("count")) {
                    int roomCount = Integer.parseInt(commandParts[3]);
                    office.configureRooms(roomCount);
                } else if (commandParts[1].equals("room") && commandParts[2].equals("max") && commandParts[3].equals("capacity")) {
                    int roomNumber = Integer.parseInt(commandParts[4]);
                    int maxCapacity = Integer.parseInt(commandParts[5]);
                    if (maxCapacity <= 0) {
                        System.out.println("Invalid capacity. Please enter a valid positive number.");
                    } else {
                        Room room = office.getRoom(roomNumber);
                        if (room != null) {
                            room.setMaxCapacity(maxCapacity);
                        } else {
                            System.out.println("Room " + roomNumber + " does not exist.");
                        }
                    }
                }
                break;
            case "block":
                int roomNumber = Integer.parseInt(commandParts[1]);
                String time = commandParts[2];
                int duration = Integer.parseInt(commandParts[3]);
                Room room = office.getRoom(roomNumber);
                if (room != null) {
                    room.bookRoom(time, duration);
                } else {
                    System.out.println("Room " + roomNumber + " does not exist.");
                }
                break;
            case "cancel":
                roomNumber = Integer.parseInt(commandParts[1]);
                room = office.getRoom(roomNumber);
                if (room != null) {
                    room.cancelBooking();
                } else {
                    System.out.println("Room " + roomNumber + " does not exist.");
                }
                break;
            case "add":
                if (commandParts[1].equals("occupant")) {
                    roomNumber = Integer.parseInt(commandParts[2]);
                    int occupants = Integer.parseInt(commandParts[3]);
                    room = office.getRoom(roomNumber);
                    if (room != null) {
                        room.addOccupant(occupants);
                    } else {
                        System.out.println("Room " + roomNumber + " does not exist.");
                    }
                }
                break;
            default:
                System.out.println("Invalid command.");
        }
    }
}
