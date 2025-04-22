# Prim-s-Algorithm-using-java
This is a code which solves prims algorithm using java
import javax.swing.*; // Importing Swing components for GUI
import java.awt.*;     // Importing AWT for graphics (Color, Point, Graphics, etc.)
import java.util.*;    // Importing utility classes (like Arrays)

public class gg extends JPanel { // Creating a JPanel subclass to draw the MST graph
    private final int[][] graph;       // The adjacency matrix of the graph
    private final boolean[][] mstEdges; // Matrix to mark which edges are part of the MST
    private final Point[] nodePositions; // Array to store the (x, y) positions of the nodes

    public gg               (int[][] graph) {
        this.graph = graph; // Initialize the graph
        this.mstEdges = new boolean[graph.length][graph.length]; // Initialize the MST edges matrix
        this.nodePositions = generateNodePositions(graph.length); // Generate positions in a circle
        primMST(); // Call Prim's algorithm to compute the MST
    }

    // Generate positions for the nodes arranged in a circle
    private Point[] generateNodePositions(int n) {
        Point[] points = new Point[n]; // Array to hold positions
        int centerX = 400, centerY = 300, radius = 200; // Define the center and radius of the circle
        for (int i = 0; i < n; i++) {
            double angle = 2 * Math.PI * i / n; // Angle in radians for circular layout
            int x = centerX + (int)(radius * Math.cos(angle)); // x-coordinate
            int y = centerY + (int)(radius * Math.sin(angle)); // y-coordinate
            points[i] = new Point(x, y); // Store the point
        }
        return points; // Return the generated positions
    }

    // Prim's Algorithm to find the Minimum Spanning Tree (MST)
    private void primMST() {
        int n = graph.length; // Number of vertices
        boolean[] visited = new boolean[n]; // Tracks visited vertices
        int[] key = new int[n]; // Cost to connect to the MST
        int[] parent = new int[n]; // Stores parent nodes in MST

        Arrays.fill(key, Integer.MAX_VALUE); // Initialize all keys to infinity
        key[0] = 0; // Start from vertex 0 with cost 0
        parent[0] = -1; // Root of the MST

        for (int count = 0; count < n - 1; count++) {
            int u = minKey(key, visited); // Find the unvisited node with the smallest key
            visited[u] = true; // Mark this node as visited

            // Check and update adjacent vertices
            for (int v = 0; v < n; v++) {
                // If there's an edge, v is unvisited, and weight is smaller than current key
                if (graph[u][v] != 0 && !visited[v] && graph[u][v] < key[v]) {
                    parent[v] = u; // Update parent
                    key[v] = graph[u][v]; // Update key
                }
            }
        }

        // Build the MST edges from parent array
        for (int i = 1; i < n; i++) {
            mstEdges[i][parent[i]] = true; // Mark edge as part of MST
            mstEdges[parent[i]][i] = true; // Mark in both directions for undirected graph
        }
    }

    // Helper to find the unvisited node with the smallest key
    private int minKey(int[] key, boolean[] visited) {
        int min = Integer.MAX_VALUE, minIndex = -1;
        for (int v = 0; v < key.length; v++) {
            if (!visited[v] && key[v] < min) {
                min = key[v]; // Update min value
                minIndex = v; // Update min index
            }
        }
        return minIndex; // Return the node with minimum key
    }

    // Paint component override to draw graph
    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g); // Call JPanel's method to ensure proper painting

        // Draw the edges
        for (int i = 0; i < graph.length; i++) {
            for (int j = i + 1; j < graph.length; j++) {
                if (graph[i][j] != 0) { // If edge exists
                    g.setColor(mstEdges[i][j] ? Color.RED : Color.LIGHT_GRAY); // MST edge in red
                    g.drawLine(nodePositions[i].x, nodePositions[i].y, nodePositions[j].x, nodePositions[j].y); // Draw edge

                    // Draw edge weight near midpoint of the line
                    int midX = (nodePositions[i].x + nodePositions[j].x) / 2;
                    int midY = (nodePositions[i].y + nodePositions[j].y) / 2;
                    g.setColor(Color.BLACK); // Text color
                    g.drawString(String.valueOf(graph[i][j]), midX + 5, midY - 5); // Print weight
                }
            }
        }

        // Draw the nodes (vertices)
        for (int i = 0; i < nodePositions.length; i++) {
            g.setColor(Color.BLUE); // Node color
            g.fillOval(nodePositions[i].x - 15, nodePositions[i].y - 15, 30, 30); // Draw circle for node

            g.setColor(Color.WHITE); // Label color
            String label = String.valueOf(i); // Node label
            FontMetrics fm = g.getFontMetrics(); // Get font metrics to center label
            int labelWidth = fm.stringWidth(label); // Label width
            int labelHeight = fm.getAscent(); // Label height
            g.drawString(label, nodePositions[i].x - labelWidth / 2, nodePositions[i].y + labelHeight / 2); // Draw label
        }

        // Draw legend for edge colors
        g.setColor(Color.RED); // Color for MST edge
        g.drawLine(20, 20, 60, 20); // Line for MST
        g.setColor(Color.BLACK);
        g.drawString("MST Edge", 65, 25); // Label

        g.setColor(Color.LIGHT_GRAY); // Color for original edge
        g.drawLine(20, 40, 60, 40); // Line for non-MST
        g.setColor(Color.BLACK);
        g.drawString("Original Edge", 65, 45); // Label
    }

    // Main method to run the visualization
    public static void main(String[] args) {
        // Sample adjacency matrix representing a weighted graph
        int[][] graph = {
                {0, 2, 0, 6, 0},
                {2, 0, 3, 8, 5},
                {0, 3, 0, 0, 7},
                {6, 8, 0, 0, 9},
                {0, 5, 7, 9, 0}
        };

        // Create the window frame
        JFrame frame = new JFrame("Prim's Algorithm - MST Visualization");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // Close app when window is closed
        frame.add(new gg(graph)); // Add our custom JPanel
        frame.setSize(800, 600); // Set window size
        frame.setLocationRelativeTo(null); // Center the window on screen
        frame.setVisible(true); // Show the window
    }
}
