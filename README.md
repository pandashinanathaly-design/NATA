# NATA
#para libro
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
package libro;
import java.sql.*;
import javax.swing.table.DefaultTableModel;

/**
 *
 * @author Admin
 */
public class LibroDao {
    public void cargarAutores(javax.swing.JComboBox combo) {
        try (Connection con = conexion.conectar(); Statement st = con.createStatement(); ResultSet rs = st.executeQuery("SELECT nombre_autor FROM autor")) {
            combo.removeAllItems();
            while (rs.next()) combo.addItem(rs.getString(1));
        } catch (Exception e) {}
    }

    public boolean insertar(String tit, int anio, String aut) {
        String sql = "INSERT INTO libros (id_libro, titulo, anio_publicacion, id_autor) SELECT ?, ?, ?, id_autor FROM autor WHERE nombre_autor = ?";
        try (Connection con = conexion.conectar(); PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setString(1, "L-" + System.currentTimeMillis());
            ps.setString(2, tit);
            ps.setInt(3, anio);
            ps.setString(4, aut);
            return ps.executeUpdate() > 0;
        } catch (Exception e) { return false; }
    }

    public DefaultTableModel buscar(String aut) {
        DefaultTableModel m = new DefaultTableModel(new String[]{"Código", "Título", "Año", "Autor"}, 0);
        String sql = "SELECT l.id_libro, l.titulo, l.anio_publicacion, a.nombre_autor FROM libros l JOIN autor a ON l.id_autor = a.id_autor WHERE a.nombre_autor = ?";
        try (Connection con = conexion.conectar(); PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setString(1, aut);
            ResultSet rs = ps.executeQuery();
            while (rs.next()) m.addRow(new Object[]{rs.getString(1), rs.getString(2), rs.getInt(3), rs.getString(4)});
        } catch (Exception e) {}
        return m;
    }
}
#####################conexion ###############
package libro;
import java.sql.*;
import javax.swing.JOptionPane;
/**
 *
 * @author Admin
 */
public class conexion {
    public static Connection conectar() {
        Connection conn = null;
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost/biblioteca", "root", "");
            JOptionPane.showMessageDialog(null, "ok esta conectado");
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(null, ex);

        }
        return conn;
    }

} 
##################prueba###############3
 public pru() {
        initComponents();
        new LibroDao().cargarAutores(jComboAutor);
    }
    public void guardar() {
        try {
            LibroDao dao = new LibroDao();
            String titulo = jTextTitulo.getText();
            int anio = Integer.parseInt(jTextAnio.getText());
            String autor = jComboAutor.getSelectedItem().toString();

            if (dao.insertar(titulo, anio, autor)) {
                javax.swing.JOptionPane.showMessageDialog(this, "Guardado con éxito");
                jTextTitulo.setText("");
                jTextAnio.setText("");
            } else {
                javax.swing.JOptionPane.showMessageDialog(this, "Error al guardar");
            }
        } catch (NumberFormatException e) {
            javax.swing.JOptionPane.showMessageDialog(this, "El año debe ser un número entero.");
        }
    }

    public void buscar() {
        LibroDao dao = new LibroDao();
        String autorSeleccionado = jComboAutor.getSelectedItem().toString();
        jTablalibros.setModel(dao.buscar(autorSeleccionado));
    }


