import javax.swing.*;
import javax.swing.border.*;
import javax.swing.table.DefaultTableModel;
import javax.swing.table.TableRowSorter;
import java.awt.*;
import java.awt.event.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.HashMap;
import java.util.Map;

public class CarePlusProModern extends JFrame {
    private final Color PRIMARY_TEAL = new Color(74, 161, 155);
    private final Color TEXT_DARK_TEAL = new Color(220, 239, 237);
    private final Color BACKGROUND_GRAY = new Color(248, 249, 250);
    private final Color TEXT_DARK = new Color(44, 62, 80);

    private JPanel cardPanel;
    private CardLayout cardLayout;
    private DefaultTableModel tableModel;
    private JTable recordTable;
    
    private JLabel totalPatientLabel, deptStatLabel;
    private JPanel chartContainer;
    private Map<String, Integer> deptCounts = new HashMap<>();

    public CarePlusProModern() {
        setTitle("CarePlus Pro | NextGen Medical Suite");
        setSize(1150, 800);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        setLayout(new BorderLayout());
        cardLayout = new CardLayout();
        cardPanel = new JPanel(cardLayout);

        setupSidebar();
        createDashboard();
        createRegistrationForm();
        createDatabaseView();
        createAnalyticsView();

        add(cardPanel, BorderLayout.CENTER);
        setVisible(true);
    }

    private void setupSidebar() {
        JPanel sidebar = new JPanel();
        sidebar.setLayout(new BoxLayout(sidebar, BoxLayout.Y_AXIS));
        sidebar.setBackground(PRIMARY_TEAL);
        sidebar.setPreferredSize(new Dimension(240, 800));
        sidebar.setBorder(new EmptyBorder(30, 15, 10, 15));

        JLabel logo = new JLabel("CAREPLUS PRO", SwingConstants.CENTER);
        logo.setForeground(Color.WHITE);
        logo.setFont(new Font("Segoe UI", Font.BOLD, 22));
        logo.setAlignmentX(Component.CENTER_ALIGNMENT);
        
        sidebar.add(logo);
        sidebar.add(Box.createRigidArea(new Dimension(0, 50)));

        String[][] navItems = {
            {"Dashboard", "Home"}, 
            {"Register Patient", "Register"}, 
            {"Medical Records", "Records"}, 
            {"System Analytics", "Analytics"}
        };

        for (String[] item : navItems) {
            JButton btn = createNavButton(item[0], item[1]);
            sidebar.add(btn);
            sidebar.add(Box.createRigidArea(new Dimension(0, 10)));
        }

        add(sidebar, BorderLayout.WEST);
    }

    private JButton createNavButton(String text, String cardName) {
        JButton btn = new JButton(text);
        btn.setMaximumSize(new Dimension(210, 45));
        btn.setFont(new Font("Segoe UI", Font.PLAIN, 14));
        btn.setFocusPainted(false);
        btn.setBackground(PRIMARY_TEAL);
        btn.setForeground(Color.WHITE);
        btn.setBorder(new EmptyBorder(10, 20, 10, 20));
        btn.setCursor(new Cursor(Cursor.HAND_CURSOR));
        btn.setHorizontalAlignment(SwingConstants.LEFT);

        btn.addActionListener(e -> cardLayout.show(cardPanel, cardName));
        return btn;
    }

    private void createDashboard() {
        JPanel home = new JPanel(new BorderLayout());
        home.setBackground(BACKGROUND_GRAY);

        JPanel hero = new JPanel(new GridBagLayout());
        hero.setBackground(TEXT_DARK_TEAL );
        hero.setPreferredSize(new Dimension(800, 200));
        
        JLabel welcome = new JLabel("Welcome to CarePlus Health Systems");
        welcome.setFont(new Font("Segoe UI Light", Font.BOLD, 32));
        hero.add(welcome);

        JPanel statsGrid = new JPanel(new GridLayout(1, 3, 25, 0));
        statsGrid.setBackground(BACKGROUND_GRAY);
        statsGrid.setBorder(new EmptyBorder(40, 40, 40, 40));
        
        totalPatientLabel = new JLabel("0", SwingConstants.CENTER);
        totalPatientLabel.setFont(new Font("Segoe UI", Font.BOLD, 28));
        
        statsGrid.add(createStatCard("Total Admitted", totalPatientLabel));
        statsGrid.add(createStatCard("System Status", new JLabel("Online", SwingConstants.CENTER)));
        statsGrid.add(createStatCard("Server Latency", new JLabel("12ms", SwingConstants.CENTER)));

        home.add(hero, BorderLayout.NORTH);
        home.add(statsGrid, BorderLayout.CENTER);
        cardPanel.add(home, "Home");
    }

    private JPanel createStatCard(String title, JLabel valLabel) {
        JPanel p = new JPanel(new BorderLayout());
        p.setBackground(Color.WHITE);
        p.setBorder(BorderFactory.createCompoundBorder(
            new LineBorder(new Color(230, 230, 230), 1),
            new EmptyBorder(20, 20, 20, 20)
        ));
        
        JLabel t = new JLabel(title);
        t.setForeground(Color.GRAY);
        valLabel.setForeground(PRIMARY_TEAL);
        
        p.add(t, BorderLayout.NORTH);
        p.add(valLabel, BorderLayout.CENTER);
        return p;
    }

    private void createRegistrationForm() {
        JPanel container = new JPanel(new GridBagLayout());
        container.setBackground(Color.WHITE);
        GridBagConstraints g = new GridBagConstraints();
        g.insets = new Insets(12, 12, 12, 12);
        g.fill = GridBagConstraints.HORIZONTAL;

        JTextField n = new JTextField(25);
        JTextField a = new JTextField(25);
        
        String[] diseases = {
            "Internal Medicine", "Cardiology - Heart Failure", "Neurology - Epilepsy", 
            "Orthopedics - Spinal", "Pediatrics - Neonatal", "Oncology - Chemotherapy", 
            "Psychiatry - Clinical", "Dermatology - Surgery", "Gastroenterology", "Emergency Trauma"
        };
        JComboBox<String> d = new JComboBox<>(diseases);
        
        JButton submit = new JButton("Confirm Registration");
        submit.setBackground(PRIMARY_TEAL);
        submit.setForeground(Color.RED);
        submit.setFont(new Font("Segoe UI", Font.BOLD, 14));
        submit.setPreferredSize(new Dimension(100, 40));

        g.gridx = 0; g.gridy = 0; container.add(new JLabel("Patient Full Name:"), g);
        g.gridx = 1; container.add(n, g);
        g.gridx = 0; g.gridy = 1; container.add(new JLabel("Age:"), g);
        g.gridx = 1; container.add(a, g);
        g.gridx = 0; g.gridy = 2; container.add(new JLabel("Specialized Department:"), g);
        g.gridx = 1; container.add(d, g);
        g.gridx = 1; g.gridy = 3; container.add(submit, g);

        submit.addActionListener(e -> {
            if(!n.getText().isEmpty()){
                String timestamp = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm"));
                String dept = (String)d.getSelectedItem();
                
                tableModel.addRow(new Object[]{n.getText(), a.getText(), dept, timestamp});
                
                deptCounts.put(dept, deptCounts.getOrDefault(dept, 0) + 1);
                updateAnalyticsDisplay();
                
                JOptionPane.showMessageDialog(this, "Record Secured Successfully.");
                n.setText(""); a.setText("");
                cardLayout.show(cardPanel, "Records");
            }
        });

        cardPanel.add(container, "Register");
    }

    private void createDatabaseView() {
        JPanel panel = new JPanel(new BorderLayout());
        panel.setBackground(Color.WHITE);
        panel.setBorder(new EmptyBorder(25, 25, 25, 25));

        tableModel = new DefaultTableModel(new String[]{"Name", "Age", "Condition/Dept", "Registration Date"}, 0);
        recordTable = new JTable(tableModel);
        recordTable.setRowHeight(40);
        recordTable.setFont(new Font("Segoe UI", Font.PLAIN, 13));
        
        panel.add(new JLabel("Live Patient Directory", SwingConstants.LEFT), BorderLayout.NORTH);
        panel.add(new JScrollPane(recordTable), BorderLayout.CENTER);
        cardPanel.add(panel, "Records");
    }

    private void createAnalyticsView() {
        JPanel panel = new JPanel(new BorderLayout());
        panel.setBackground(BACKGROUND_GRAY);
        
        JPanel header = new JPanel(new BorderLayout());
        header.setBackground(PRIMARY_TEAL);
        header.setPreferredSize(new Dimension(1000, 80));
        JLabel title = new JLabel("  Hospital Workload Analytics", SwingConstants.LEFT);
        title.setForeground(Color.WHITE);
        title.setFont(new Font("Segoe UI", Font.BOLD, 20));
        header.add(title, BorderLayout.CENTER);

        chartContainer = new JPanel();
        chartContainer.setLayout(new BoxLayout(chartContainer, BoxLayout.Y_AXIS));
        chartContainer.setBackground(Color.WHITE);
        chartContainer.setBorder(new EmptyBorder(30, 50, 30, 50));

        panel.add(header, BorderLayout.NORTH);
        panel.add(new JScrollPane(chartContainer), BorderLayout.CENTER);
        cardPanel.add(panel, "Analytics");
    }

    private void updateAnalyticsDisplay() {
       
        totalPatientLabel.setText(String.valueOf(tableModel.getRowCount()));
        
     
        chartContainer.removeAll();
        for (String dept : deptCounts.keySet()) {
            int count = deptCounts.get(dept);
            JPanel barRow = new JPanel(new BorderLayout());
            barRow.setMaximumSize(new Dimension(900, 50));
            barRow.setBackground(Color.WHITE);
            
            JLabel name = new JLabel(dept + " (" + count + ")");
            name.setPreferredSize(new Dimension(200, 30));
            
            JProgressBar bar = new JProgressBar(0, 20);
            bar.setValue(count);
            bar.setForeground(PRIMARY_TEAL);
            
            barRow.add(name, BorderLayout.WEST);
            barRow.add(bar, BorderLayout.CENTER);
            chartContainer.add(barRow);
            chartContainer.add(Box.createRigidArea(new Dimension(0, 15)));
        }
        chartContainer.revalidate();
        chartContainer.repaint();
    }

    public static void main(String[] args) {
        try { UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName()); } 
        catch (Exception e) {}
        new CarePlusProModern();
    }
}
