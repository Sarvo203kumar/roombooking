import java.util.ArrayList;
import java.util.List;

class OfficeConfiguration {
    private static OfficeConfiguration instance;
    private List<ConferenceRoom> rooms;

    private OfficeConfiguration() {
        rooms = new ArrayList<>();
    }

    public static OfficeConfiguration getInstance() {
        if (instance == null) {
            instance = new OfficeConfiguration();
        }
        return instance;
    }

    public void configureOffice(int numberOfRooms) {
        rooms.clear();
        for (int i = 1; i <= numberOfRooms; i++) {
            rooms.add(new ConferenceRoom("Room " + i));
        }
        System.out.println("Office configured with " + numberOfRooms + " meeting rooms: " + getRoomNames() + ".");
    }

    public void setRoomCapacity(String roomName, int capacity) {
        ConferenceRoom room = getRoom(roomName);
        if (room != null && capacity > 0) {
            room.setMaxCapacity(capacity);
            System.out.println(roomName + " maximum capacity set to " + capacity + ".");
        } else {
            System.out.println("Invalid capacity. Please enter a valid positive number.");
        }
    }

    public List<ConferenceRoom> getRooms() {
        return rooms;
    }

    public ConferenceRoom getRoom(String roomName) {
        for (ConferenceRoom room : rooms) {
            if (room.getName().equals(roomName)) {
                return room;
            }
        }
        return null;
    }

    private String getRoomNames() {
        StringBuilder names = new StringBuilder();
        for (ConferenceRoom room : rooms) {
            names.append(room.getName()).append(", ");
        }
        return names.substring(0, names.length() - 2);
    }
}
