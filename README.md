# SCT_SD_04
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.io.FileWriter;
import java.util.ArrayList;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.select.Elements;
import com.opencsv.CSVWriter;

public class ProductScraperGUI extends JFrame {
    private JTextField urlField;
    private JButton scrapeButton;
    private JLabel statusLabel;

    public ProductScraperGUI() {
        setTitle("E-commerce Product Scraper");
        setSize(500, 200);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(new FlowLayout());

        urlField = new JTextField(30);
        scrapeButton = new JButton("Scrape Data");
        statusLabel = new JLabel("");

        scrapeButton.addActionListener(e -> scrapeData());

        add(new JLabel("Enter URL:"));
        add(urlField);
        add(scrapeButton);
        add(statusLabel);
    }

    private void scrapeData() {
        String url = urlField.getText();
        try {
            Document doc = Jsoup.connect(url).get();
            Elements names = doc.select(".product-name-selector"); // Change selectors
            Elements prices = doc.select(".price-selector");
            Elements ratings = doc.select(".rating-selector");

            CSVWriter writer = new CSVWriter(new FileWriter("products.csv"));
            writer.writeNext(new String[]{"Product Name", "Price", "Rating"});

            for (int i = 0; i < names.size(); i++) {
                String name = names.get(i).text();
                String price = prices.get(i).text();
                String rating = ratings.get(i).text();
                writer.writeNext(new String[]{name, price, rating});
            }
            writer.close();
            statusLabel.setText("Scraping complete! Data saved to products.csv.");
        } catch (Exception ex) {
            statusLabel.setText("Error: " + ex.getMessage());
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new ProductScraperGUI().setVisible(true));
    }
}

