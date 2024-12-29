# -Creando-un-CRUD-con-persistencia-de-datos
CREATE DATABASE taller_crud;

USE taller_crud;

CREATE TABLE estudiantes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    edad INT NOT NULL
    import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DatabaseConnection {
    private static final String URL = "jdbc:mysql://localhost:3306/taller_crud";
    private static final String USER = "root"; // Cambia según tu configuración
    private static final String PASSWORD = "tu_contraseña"; // Cambia según tu configuración

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }
}public class Estudiante {
    private int id;
    private String nombre;
    private String email;
    private int edad;

    // Constructor, getters y setters
    public Estudiante(int id, String nombre, String email, int edad) {
        this.id = id;
        this.nombre = nombre;
        this.email = email;
        this.edad = edad;
    }

    // Getters y Setters
    // ...
}import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class EstudianteDAO {
    public void create(Estudiante estudiante) {
        String sql = "INSERT INTO estudiantes (nombre, email, edad) VALUES (?, ?, ?)";
        try (Connection conn = DatabaseConnection.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setString(1, estudiante.getNombre());
            pstmt.setString(2, estudiante.getEmail());
            pstmt.setInt(3, estudiante.getEdad());
            pstmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public List<Estudiante> read() {
        List<Estudiante> estudiantes = new ArrayList<>();
        String sql = "SELECT * FROM estudiantes";
        try (Connection conn = DatabaseConnection.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {
            while (rs.next()) {
                Estudiante estudiante = new Estudiante(
                        rs.getInt("id"),
                        rs.getString("nombre"),
                        rs.getString("email"),
                        rs.getInt("edad")
                );
                estudiantes.add(estudiante);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return estudiantes;
    }

    public void update(Estudiante estudiante) {
        String sql = "UPDATE estudiantes SET nombre = ?, email = ?, edad = ? WHERE id = ?";
        try (Connection conn = DatabaseConnection.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setString(1, estudiante.getNombre());
            pstmt.setString(2, estudiante.getEmail());
            pstmt.setInt(3, estudiante.getEdad());
            pstmt.setInt(4, estudiante.getId());
            pstmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void delete(int id) {
        String sql = "DELETE FROM estudiantes WHERE id = ?";
        try (Connection conn = DatabaseConnection.getConnection();
             PreparedStatement pstmt = conn.prepareStatement(sql)) {
            pstmt.setInt(1, id);
            pstmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}import java ```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        EstudianteDAO estudianteDAO = new EstudianteDAO();

        // Crear un nuevo estudiante
        Estudiante nuevoEstudiante = new Estudiante(0, "Juan Pérez", "juan.perez@example.com", 20);
        estudianteDAO.create(nuevoEstudiante);

        // Leer todos los estudiantes
        List<Estudiante> estudiantes = estudianteDAO.read();
        for (Estudiante estudiante : estudiantes) {
            System.out.println("ID: " + estudiante.getId() + ", Nombre: " + estudiante.getNombre() +
                    ", Email: " + estudiante.getEmail() + ", Edad: " + estudiante.getEdad());
        }

        // Actualizar un estudiante
        if (!estudiantes.isEmpty()) {
            Estudiante estudianteParaActualizar = estudiantes.get(0);
            estudianteParaActualizar.setNombre("Juan Pérez Actualizado");
            estudianteDAO.update(estudianteParaActualizar);
        }

        // Eliminar un estudiante
        if (!estudiantes.isEmpty()) {
            estudianteDAO.delete(estudiantes.get(0).getId());
        }
    }
}
