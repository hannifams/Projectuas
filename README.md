# Projectuas
import java.awt.event.*;
import java.sql.*;
import javax.swing.*;
import javax.swing.table.DefaultTableModel;

public class FormBarang extends JFrame {
    private String[] judul = {"Kode Barang", "Nama Barang", "Harga Barang", "Stok Barang"};
    DefaultTableModel df;
    JTable tab = new JTable();
    JScrollPane scp = new JScrollPane();
    JPanel pnl = new JPanel();
    JLabel lblkode = new JLabel("Kode Barang");
    JTextField txkode = new JTextField(10);
    JLabel lblnama = new JLabel("Nama Barang");
    JTextField txnama = new JTextField(20);
    JLabel lblharga = new JLabel("Harga Barang");
    JTextField txharga = new JTextField(10);
    JLabel lblstok = new JLabel("Stok Barang");
    JTextField txstok = new JTextField(10);
    JButton btadd = new JButton("Simpan");
    JButton btnew = new JButton("Baru");
    JButton btdel = new JButton("Hapus");
    JButton btedit = new JButton("Ubah");

    FormBarang() {
        super("Data Barang");
        setSize(500, 350);
        pnl.setLayout(null);
        
        pnl.add(lblkode);
        lblkode.setBounds(20, 10, 100, 20);
        pnl.add(txkode);
        txkode.setBounds(125, 10, 100, 20);
        
        pnl.add(lblnama);
        lblnama.setBounds(20, 40, 100, 20);
        pnl.add(txnama);
        txnama.setBounds(125, 40, 175, 20);
        
        pnl.add(lblharga);
        lblharga.setBounds(20, 70, 100, 20);
        pnl.add(txharga);
        txharga.setBounds(125, 70, 100, 20);
        
        pnl.add(lblstok);
        lblstok.setBounds(20, 100, 100, 20);
        pnl.add(txstok);
        txstok.setBounds(125, 100, 100, 20);
        
        pnl.add(btnew);
        btnew.setBounds(300, 10, 125, 20);
        btnew.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                btnewAksi(e);
            }
        });

        pnl.add(btadd);
        btadd.setBounds(300, 40, 125, 20);
        btadd.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                btaddAksi(e);
            }
        });

        pnl.add(btedit);
        btedit.setBounds(300, 70, 125, 20);
        btedit.setEnabled(false);
        btedit.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                bteditAksi(e);
            }
        });

        pnl.add(btdel);
        btdel.setBounds(300, 100, 125, 20);
        btdel.setEnabled(false);
        btdel.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                btdelAksi(e);
            }
        });

        df = new DefaultTableModel(null, judul);
        tab.setModel(df);
        scp.getViewport().add(tab);
        tab.setEnabled(true);
        tab.addMouseListener(new MouseAdapter() {
            public void mouseClicked(MouseEvent evt) {
                tabMouseClicked(evt);
            }
        });

        scp.setBounds(20, 140, 450, 150);
        pnl.add(scp);
        getContentPane().add(pnl);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setVisible(true);
    }

    void loadData() {
        try {
            Connection cn = new connecDB().getConnect();
            Statement st = cn.createStatement();
            String sql = "SELECT * FROM tbl_barang";
            ResultSet rs = st.executeQuery(sql);
            while (rs.next()) {
                String kode_barang, nama_barang, harga_barang, stok_barang;
                kode_barang = rs.getString("kode_barang");
                nama_barang = rs.getString("nama_barang");
                harga_barang = rs.getString("harga_barang");
                stok_barang = rs.getString("stok_barang");
                String[] data = {kode_barang, nama_barang, harga_barang, stok_barang};
                df.addRow(data);
            }
            rs.close();
            cn.close();
        } catch (SQLException ex) {
            ex.printStackTrace();
        }
    }

    void clearTable() {
        int numRow = df.getRowCount();
        for (int i = 0; i < numRow; i++) {
            df.removeRow(0);
        }
    }

    void clearTextField() {
        txkode.setText(null);
        txnama.setText(null);
        txharga.setText(null);
        txstok.setText(null);
    }

    void simpanData(Barang B) {
        try {
            Connection cn = new connecDB().getConnect();
            Statement st = cn.createStatement();
            String sql = "INSERT INTO tbl_barang (kode_barang, nama_barang, harga_barang, stok_barang) " +
                    "VALUES ('" + B.getKode() + "', '" + B.getNama() + "', '" + B.getHarga() + "', '" + B.getStok() + "')";
            int result = st.executeUpdate(sql);
            cn.close();
            JOptionPane.showMessageDialog(null, "Data Berhasil Disimpan",
                    "Info Proses", JOptionPane.INFORMATION_MESSAGE);
            String[] data = {B.getKode(), B.getNama(), B.getHarga(), B.getStok()};
            df.addRow(data);
        } catch (SQLException ex) {
            ex.printStackTrace();
        }
    }

    void hapusData(String kode) {
        try {
            Connection cn = new connecDB().getConnect();
            Statement st = cn.createStatement();
            String sql = "DELETE FROM tbl_barang WHERE kode_barang = '" + kode + "'";
            int result = st.executeUpdate(sql);
            cn.close();
            JOptionPane.showMessageDialog(null, "Data Berhasil Dihapus", "Info Proses",
                    JOptionPane.INFORMATION_MESSAGE);
            df.removeRow(tab.getSelectedRow());
            clearTextField();
        } catch (SQLException ex) {
            ex.printStackTrace();
        }
    }

    void ubahData(Barang B, String kode) {
        try {
            Connection cn = new connecDB().getConnect();
            Statement st = cn.createStatement();
            String sql = "UPDATE tbl_barang SET kode_barang='" + B.getKode() + "', nama_barang='" + B.getNama() +
                    "', harga_barang='" + B.getHarga() + "', stok_barang='" + B.getStok() + "' WHERE kode_barang='" + kode + "'";
            int result = st.executeUpdate(sql);
            cn.close();
            JOptionPane.showMessageDialog(null, "Data Berhasil Diubah", "Info Proses",
                    JOptionPane.INFORMATION_MESSAGE);
            clearTable();
            loadData();
        } catch (SQLException ex) {
            ex.printStackTrace();
        }
    }

    private void btnewAksi(ActionEvent evt) {
        clearTextField();
        btedit.setEnabled(false);
        btdel.setEnabled(false);
        btadd.setEnabled(true);
    }

    private void btaddAksi(ActionEvent evt) {
        Barang B = new Barang();
        B.setKode(txkode.getText());
        B.setNama(txnama.getText());
        B.setHarga(txharga.getText());
        B.setStok(txstok.getText());
        simpanData(B);
    }

    private void btdelAksi(ActionEvent evt) {
        int status;
        status = JOptionPane.showConfirmDialog(null, "Yakin data akan dihapus?",
                "Konfirmasi", JOptionPane.OK_CANCEL_OPTION);
        if (status == 0) {
            hapusData(txkode.getText());
        }
    }

    private void bteditAksi(ActionEvent evt) {
        Barang B = new Barang();
        B.setKode(txkode.getText());
        B.setNama(txnama.getText());
        B.setHarga(txharga.getText());
        B.setStok(txstok.getText());
        ubahData(B, tab.getValueAt(tab.getSelectedRow(), 0).toString());
    }

    private void tabMouseClicked(MouseEvent evt) {
        btedit.setEnabled(true);
        btdel.setEnabled(true);
        btadd.setEnabled(false);
        String kode, nama, harga, stok;
        kode = tab.getValueAt(tab.getSelectedRow(), 0).toString();
        nama = tab.getValueAt(tab.getSelectedRow(), 1).toString();
        harga = tab.getValueAt(tab.getSelectedRow(), 2).toString();
        stok = tab.getValueAt(tab.getSelectedRow(), 3).toString();
        txkode.setText(kode);
        txnama.setText(nama);
        txharga.setText(harga);
        txstok.setText(stok);
    }

    public static void main(String[] args) {
        new FormBarang().loadData();
    }
}



class Barang {
    private String kode, nama, harga, stok;

    public String getKode() {
        return kode;
    }

    public void setKode(String kode) {
        this.kode = kode;
    }

    public String getNama() {
        return nama;
    }

    public void setNama(String nama) {
        this.nama = nama;
    }

    public String getHarga() {
        return harga;
    }

    public void setHarga(String harga) {
        this.harga = harga;
    }

    public String getStok() {
        return stok;
    }

    public void setStok(String stok) {
        this.stok = stok;
    }
}



//ConnectDB.java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class connecDB {
    private String url, usr, pwd, db;
    connecDB(){
        db = "toko";
        url = "jdbc:mysql://localhost/" + db;
        usr = "root";
        pwd = "";
    }
    
    Connection getConnect(){
        Connection cn = null;
        try {
            //load driver database
            Class.forName("com.mysql.cj.jdbc.Driver");
            cn = DriverManager.getConnection(url,usr, pwd);
            System.out.println("Koneksi Berhasil");
        } catch (ClassNotFoundException er) {
            System.out.println("Error #1: " + er.getMessage());
            System.exit(0);
        } catch (SQLException er) {
            System.out.println("Error #2: " + er.getMessage());
        }
        return cn;
    }
    public static void main(String [] args) {
        new connecDB().getConnect();
    }
}
