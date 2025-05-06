# joguin
e isso
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

public class CarGame extends JPanel implements ActionListener, KeyListener {
    private Timer timer;
    private int carX, carY, carSpeed;
    private ArrayList<Rectangle> obstacles;
    private boolean left, right, turbo;
    private int turboTimer, score;
    
    // Constantes
    private final int PANEL_WIDTH = 400;
    private final int PANEL_HEIGHT = 600;
    private final int CAR_WIDTH = 40;
    private final int CAR_HEIGHT = 80;
    private final int OBSTACLE_WIDTH = 40;
    private final int OBSTACLE_HEIGHT = 60;

    public CarGame() {
        setPreferredSize(new Dimension(PANEL_WIDTH, PANEL_HEIGHT));
        setBackground(Color.BLACK);
        addKeyListener(this);
        setFocusable(true);

        carX = PANEL_WIDTH / 2 - CAR_WIDTH / 2;
        carY = PANEL_HEIGHT - CAR_HEIGHT - 10;
        carSpeed = 5;

        obstacles = new ArrayList<>();
        turboTimer = 0;
        score = 0;

        timer = new Timer(10, this);
        timer.start();
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);

        // Desenha o carro
        g.setColor(Color.RED);
        g.fillRect(carX, carY, CAR_WIDTH, CAR_HEIGHT);

        // Desenha os obstáculos
        g.setColor(Color.GREEN);
        for (Rectangle obstacle : obstacles) {
            g.fillRect(obstacle.x, obstacle.y, OBSTACLE_WIDTH, OBSTACLE_HEIGHT);
        }

        // Desenha o turbo
        if (turbo) {
            g.setColor(Color.CYAN);
            g.fillRect(carX - 10, carY + CAR_HEIGHT, 20, 20);
            g.fillRect(carX + CAR_WIDTH, carY + CAR_HEIGHT, 20, 20);
        }

        // Desenha a pontuação
        g.setColor(Color.WHITE);
        g.drawString("Pontuação: " + score, 10, 20);

        // Desenha a barra de turbo
        g.setColor(Color.GRAY);
        g.fillRect(10, 30, 100, 10);
        g.setColor(Color.CYAN);
        g.fillRect(10, 30, (turboTimer / 120.0) * 100, 10);

        // Atualiza o jogo
        Toolkit.getDefaultToolkit().sync();
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        // Movimenta os obstáculos
        for (int i = 0; i < obstacles.size(); i++) {
            Rectangle obstacle = obstacles.get(i);
            obstacle.y += 4;

            // Verifica se o carro colidiu com o obstáculo
            if (obstacle.intersects(new Rectangle(carX, carY, CAR_WIDTH, CAR_HEIGHT))) {
                JOptionPane.showMessageDialog(this, "Fim de jogo! Pontuação: " + score);
                System.exit(0);
            }
        }

        // Remove obstáculos que saíram da tela
        obstacles.removeIf(obstacle -> obstacle.y > PANEL_HEIGHT);

        // Adiciona um novo obstáculo aleatoriamente
        if (Math.random() < 0.02) {
            int x = (int) (Math.random() * (PANEL_WIDTH - OBSTACLE_WIDTH));
            obstacles.add(new Rectangle(x, -OBSTACLE_HEIGHT, OBSTACLE_WIDTH, OBSTACLE_HEIGHT));
            score++;
        }

        // Acelera o carro quando o turbo está ativo
        if (turbo && turboTimer > 0) {
            carSpeed = 10;
            turboTimer--;
        } else {
            turbo = false;
            carSpeed = 5;
        }

        // Atualiza a tela
        repaint();
    }

    @Override
    public void keyTyped(KeyEvent e) {
        // Não usado
    }

    @Override
    public void keyPressed(KeyEvent e) {
        int key = e.getKeyCode();

        if (key == KeyEvent.VK_LEFT && carX > 0) {
            left = true;
        }
        if (key == KeyEvent.VK_RIGHT && carX < PANEL_WIDTH - CAR_WIDTH) {
            right = true;
        }
        if (key == KeyEvent.VK_SPACE && turboTimer <= 0) {
            turbo = true;
            turboTimer = 120;
        }
    }

    @Override
    public void keyReleased(KeyEvent e) {
        int key = e.getKeyCode();

        if (key == KeyEvent.VK_LEFT) {
            left = false;
        }
        if (key == KeyEvent.VK_RIGHT) {
            right = false;
        }
    }

    public void update() {
        if (left) {
            carX -= carSpeed;
        }
        if (right) {
            carX += carSpeed;
        }
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("Jogo de Carro - Felipe Rodrigues");
        CarGame game = new CarGame();
        frame.add(game);
        frame.pack();
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);

        // Atualiza o estado do jogo a cada 10ms
        new Timer(10, e -> game.update()).start();
    }
}
