// importing libraries and packages for the swing application and other utilities
import java.awt.Color;
import java.awt.Font;
import java.util.*;

import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.SwingConstants;
import javax.swing.Timer;

import java.awt.event.*;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileWriter;
import java.io.IOException;
//importing colour schemes and themes
import Colours.ColourScheme;
import Sounds.SoundUtility;
import Themes.Theme;

//implements key listener because key input is implemented
public class Game implements KeyListener{

//settings up flag variables
    boolean playAgainFlag = false;
    boolean askingPlayAgain = false;
//setting up variables that represent the game window, buttons, values that will be used, etc.
    JFrame colourWindow;
    JButton butColour1;
    JLabel but1;
    JButton butColour2;
    JLabel but2;
    JButton butColour3;
    JLabel but3;
    JButton butColour4;
    JLabel but4;

    JLabel scoreLabel;

    JLabel promptAnswer;

    double time;

    int score = 0;

    ColourScheme scheme;
    boolean hardMode;
    boolean soundToggle;

    Theme theme;

    List<Integer> sequence;
    Integer answerTerm;

    //game is passed all of the parameters so it can used the selected colour scheme, hard mode toggle, and sound toggle
    //runGame method  
    public void runGame(ColourScheme selectedColourScheme, boolean isHard, boolean isSound, Theme selectedTheme) {

        //defining the settings as those determined by the user
        scheme = selectedColourScheme;
        hardMode = isHard;
        soundToggle = isSound;
        theme = selectedTheme;

        //setting the score to 0
        score = 0;

        //setting custom taskbar icon
        ImageIcon img = new ImageIcon("src/Images/icon.png");
        
        //setting up the game window's title, size, etc.
        colourWindow = new JFrame("You are playing!");
        colourWindow.setSize(1280, 720);
        colourWindow.setLocationRelativeTo(null);
        colourWindow.setLayout(null);
        colourWindow.setIconImage(img.getImage());
        colourWindow.addKeyListener(this);

        //setting up the current score label's font, colour, size, etc. and adding it to colourWindow
        scoreLabel = new JLabel();
        scoreLabel.setText("<html><center>SCORE: " + score + "</center></html>");
        scoreLabel.setHorizontalAlignment(SwingConstants.CENTER);
        scoreLabel.setBounds(540, 20, 200, 50);
        scoreLabel.setFont(new Font("Bahnschrift", Font.PLAIN, 20));
        scoreLabel.setOpaque(true);
        scoreLabel.setBackground(Color.decode(scheme.labelBackHex));
        scoreLabel.setForeground(Color.decode(scheme.labelForeHex));
        colourWindow.getContentPane().add(scoreLabel);

        //setting up the answer prompt and adding it to colourWindow
        promptAnswer = new JLabel();
        promptAnswer.setText("What was the sequence?");
        promptAnswer.setFont(new Font("Bahnschrift", Font.PLAIN, 30));
        promptAnswer.setHorizontalAlignment(SwingConstants.CENTER);
        promptAnswer.setBounds(440, 150, 400, 100);
        promptAnswer.setVisible(false);
        colourWindow.getContentPane().add(promptAnswer);

        //ensuring that if the game window is closed prematurely, the main menu window is reopened.
        colourWindow.addWindowListener(new java.awt.event.WindowAdapter() {
            @Override
            public void windowClosing(java.awt.event.WindowEvent windowEvent) {
                if (playAgainFlag == false) {
                    try {
                        Menu mainMenu = Menu.getInstance();
                        mainMenu.mainMenu(scheme, isHard, soundToggle, theme);
                        mainMenu.playBackgroundMusic();
                        mainMenu.updateScore();
                    } catch (Exception e1) {
                        e1.printStackTrace();
                    }
                }
            }
        });
        //button 1's position, size, colour (defined by the user-selected colour theme), etc
        butColour1 = new JButton();
        butColour1.setSize(100, 100);
        butColour1.setBackground(Color.decode(scheme.colourHex1));
        colourWindow.getContentPane().add(butColour1);
        butColour1.setLocation(290,310);
        butColour1.setVisible(false);
        butColour1.setFocusable(false);
        
        //making the input of button 1 result in an answer value of 1 
        butColour1.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e){
                answer(1);
            }
        });
        
        //a label that denotes the number of the button
        but1 = new JLabel("1");
        but1.setHorizontalAlignment(SwingConstants.CENTER);
        but1.setFont(new Font("Bahnschrift", Font.BOLD, 14));
        colourWindow.getContentPane().add(but1);
        but1.setBounds(315, 425, 50,50);
        but1.setVisible(false);

        //button 2's position, size, colour (defined by the user-selected colour theme), etc
        butColour2 = new JButton();
        butColour2.setSize(100, 100);
        butColour2.setBackground(Color.decode(scheme.colourHex2));
        colourWindow.getContentPane().add(butColour2);
        butColour2.setLocation(490, 310);
        butColour2.setVisible(false);
        butColour2.setFocusable(false);
        //making the input of button 2 result in an answer value of 2 
        butColour2.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e){
                answer(2);
            }
        });
        //a label that denotes the number of the button
        but2 = new JLabel("2");
        but2.setHorizontalAlignment(SwingConstants.CENTER);
        but2.setFont(new Font("Bahnschrift", Font.BOLD, 14));
        colourWindow.getContentPane().add(but2);
        but2.setBounds(515, 425, 50,50);
        but2.setVisible(false);

        //button 3's position, size, colour (defined by the user-selected colour theme), etc
        butColour3 = new JButton();
        butColour3.setSize(100, 100);
        butColour3.setBackground(Color.decode(scheme.colourHex3));
        colourWindow.getContentPane().add(butColour3);
        butColour3.setLocation(690,310);
        butColour3.setVisible(false);
        butColour3.setFocusable(false);
        //making the input of button 3 result in an answer value of 3
        butColour3.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e){
                answer(3);
            }
        });

        //a label that denotes the number of the button
        but3 = new JLabel("3");
        but3.setHorizontalAlignment(SwingConstants.CENTER);
        but3.setFont(new Font("Bahnschrift", Font.BOLD, 14));
        colourWindow.getContentPane().add(but3);
        but3.setBounds(715, 425, 50,50);
        but3.setVisible(false);

        //button 4's position, size, colour (defined by the user-selected colour theme), etc
        butColour4 = new JButton();
        butColour4.setSize(100, 100);
        butColour4.setBackground(Color.decode(scheme.colourHex4));
        colourWindow.getContentPane().add(butColour4);
        butColour4.setLocation(890,310);
        butColour4.setVisible(false);
        butColour4.setFocusable(false);
        //making the input of button 4 result in an answer value of 4
        butColour4.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e){
                answer(4);
            }
        });

        //a label that denotes the number of the button
        but4 = new JLabel("4");
        but4.setHorizontalAlignment(SwingConstants.CENTER);
        but4.setFont(new Font("Bahnschrift", Font.BOLD, 14));
        colourWindow.getContentPane().add(but4);
        but4.setBounds(915, 425, 50,50);
        but4.setVisible(false);

        //makes the game window visible
        colourWindow.setVisible(true);

        //creates a new list "sequence," which stores the answer values correspending to the colours shown, in the sequence they appear.
        sequence = new LinkedList<Integer>();
        //add an int 1-4 to the list
        sequence.add((int)(Math.random()*4 + 1));

       //calling the nextRound method to begin the next round
        nextRound();
    }

    //the next round method hides the buttons from when the user answered, resets the answer term counter, and adds a colour to the sequence without letting the same colour be added consecutively (it would be too confusing)
    //this method is basically an intermediate step between round end and round start
    public void nextRound() {
        //hiding the buttons at the start of a round
        promptAnswer.setVisible(false);
        butColour1.setVisible(false);
        but1.setVisible(false);
        butColour2.setVisible(false);
        but2.setVisible(false);
        butColour3.setVisible(false);
        but3.setVisible(false);
        butColour4.setVisible(false);
        but4.setVisible(false);
        
        //answerTerm keeps track of which answer the user is on.
        answerTerm = 0;
        //don't allow same colour twice in a row
        int newColour = (int)(Math.random()*4 + 1);
        while (newColour == sequence.get(sequence.size()-1))
        {
            newColour = (int)(Math.random()*4 + 1);
        }
        //add the new colour to sequence
        sequence.add(newColour);

        //reseting the flag that stops game over window from opening multiple times
        playAgainFlag = false;

        //call do round
        doRound();
    }
    
    //make the colour counter variable which is used to count which term of the sequence is being displayed
    int colourCounter = 0;

    public void doRound(){
        //reset the colour counter
        colourCounter = 0;
        //this portion is put in action listener so that the timer (below) can put time in between colour changes
        ActionListener taskPerformer = new ActionListener() {
            public void actionPerformed(ActionEvent evt) {
                //if the entire sequence has been displayed
                if (colourCounter == sequence.size()) {
                    //set background colour to white
                    colourWindow.getContentPane().setBackground(Color.white);
                    //stop timer
                    ((Timer)evt.getSource()).stop();

                    //make the answer buttons & prompt visible;
                    promptAnswer.setVisible(true);
                    butColour1.setVisible(true);
                    but1.setVisible(true);
                    butColour2.setVisible(true);
                    but2.setVisible(true);
                    butColour3.setVisible(true);
                    but3.setVisible(true);
                    butColour4.setVisible(true);
                    but4.setVisible(true);

                    return;
                }
                //play the corresponding beep sound that matches the colour being shown
                //beep for colour 1
                if (sequence.get(colourCounter) == 1){
                    if (soundToggle) {
                        SoundUtility beep = new SoundUtility();
                        beep.playSound(SoundUtility.Sounds.colour1);
                    }
                    colourWindow.getContentPane().setBackground(Color.decode(scheme.colourHex1));
                //beep for colour 2
                } else if (sequence.get(colourCounter) == 2){
                    if (soundToggle) {
                        SoundUtility beep = new SoundUtility();
                        beep.playSound(SoundUtility.Sounds.colour2);
                    }
                    colourWindow.getContentPane().setBackground(Color.decode(scheme.colourHex2));
                //beep for colour 3
                } else if (sequence.get(colourCounter) == 3){
                    if (soundToggle) {
                        SoundUtility beep = new SoundUtility();
                        beep.playSound(SoundUtility.Sounds.colour3);
                    }
                    colourWindow.getContentPane().setBackground(Color.decode(scheme.colourHex3));
                //beep for colour 4
                } else {
                    if (soundToggle) {
                        SoundUtility beep = new SoundUtility();
                        beep.playSound(SoundUtility.Sounds.colour4);
                    }
                    colourWindow.getContentPane().setBackground(Color.decode(scheme.colourHex4));
                }
                colourWindow.invalidate(); //makes the colourwindow repaint to screen

                //increasing colour counter
                colourCounter++;
                
            }
        };
        //default time between colour flashes
        time = 1000;

        //if the game is set to hard mode, the time between flashes will decreased based on a rational function
            //in this rational function the independent variable (x) is represented by the score variable... so on round 1 the x = 0
                //the y-int of this function therefore represents the milliseconds given on round 1, approx 1000 (or 1 sec)
                //the function's horizontal asymptote is at y = 250, so the colours never switch faster than a quarter of a second (we determined this is the hardest time that is still playable)
                //this rational function allows us to have an infinitely scaling time system
        if (hardMode) {
            time = (((5000) / ((2 * score) + 6.5)) + 250);
        }
        //starts the timer
        Timer timer = new Timer((int) time ,taskPerformer);
        timer.setRepeats(true);
        timer.start();
    }
    //method to check if the user's answer matches the next term in the sequence
    private void checkAnswer(int buttonChosen){
        //if the user got it wrong already, dont do any of this... stops the player as soon as they are wrong so that game over window doesnt come up many times
        if (askingPlayAgain) {
            return;
        }
        //if the player's answer matches the next term in the sequence
        if (sequence.get(answerTerm) == buttonChosen){
            //counter for total terms in the sequence
            int totalTerms = (sequence.size() - 1);
            //if the user answers the final term in the sequence
            if (answerTerm == totalTerms){
                // increase score, play a success sound, update the score label, move to the next round after a delay (so no sounds overlap)
                score ++;
                Timer delayTimer = new Timer(500, new ActionListener() {
                    @Override
                    public void actionPerformed(ActionEvent e) {
                        if (soundToggle) {
                            SoundUtility beep = new SoundUtility();
                            beep.playSound(SoundUtility.Sounds.good);
                        }
                        scoreLabel.setText("Score: " + score);
                        nextRound();
                    }
                });
                delayTimer.setRepeats(false);
                delayTimer.start();
            }
            //otherwise, it means they still have more answer terms left for the current sequence, so increase the counter for which term they're answering and let them press another button
            else {
                answerTerm++;
            }
        }
        else {
            //has to be here because this is what prevents spamming incorrect button from opening gameOver multiple times, can't have delay
            askingPlayAgain = true;
            //timer delay before ending the game
            Timer delayTimer = new Timer(300, new ActionListener() {
                @Override
                public void actionPerformed(ActionEvent e) {
                    endGame();
                }
            });
            //ensure the timer only triggers once
            delayTimer.setRepeats(false);
            delayTimer.start();
        }
    }

    //what happens when they lose
    public void endGame() {
        //reset play again flag
        playAgainFlag = false;

        //if sound is enabled, play the lose sound effect
        if (soundToggle) {
            SoundUtility beep = new SoundUtility();
            beep.playSound(SoundUtility.Sounds.bad);
        }

        // check if new high score
        // open file and read it
        String path = (System.getProperty("user.dir") + "\\savedScores.txt");
        File file = new File(path);

        //must set to null at first otherwise code thinks they may not be set (if file doesn't exist)
        String[] first = null;
        String[] second = null;
        String[] third = null;

        if (file.exists()) {
            Scanner reader;
            try {
                //read from file and store the top three scores
                reader = new Scanner(file);
                if (reader.hasNext()) {
                    first = reader.nextLine().split(";");
                }
                if (reader.hasNext()) {
                    second = reader.nextLine().split(";");
                }
                if (reader.hasNext()) {
                    third = reader.nextLine().split(";");
                }
                reader.close();
            } catch (FileNotFoundException e1) {
                e1.printStackTrace();
            }
        }
        //compare scores in file with current score
        int rank = 0;

        if (first == null || score > Integer.parseInt(first[1])) {
            rank = 1;
        }
        else if (second == null || score > Integer.parseInt(second[1])) {
            rank = 2;
        }
        else if (third == null || score > Integer.parseInt(third[1])) {
            rank =3;
        }
        
        //varibles for player score data
        String playerName = "";
        String scoreData = "";
        //check if the current score qualifies for a high score position
    //if its a high score, ask for name
        //player is #1
        if (rank == 1) {
            playerName = JOptionPane.showInputDialog("High score! You made first place! Enter your name:");

            scoreData = (playerName + ";" + score);
        //these statements account for if only first exists and second doesn't or otherwise
            if (first != null) {
                //first gets kicked to second
                scoreData += "\n" + first[0] + ";" + first[1];
            }
            if (second != null) {
                //second becomes third
                scoreData += "\n" + second[0] + ";" + second[1];
            }
        }
        //player is #2
        else if (rank == 2) {
            playerName = JOptionPane.showInputDialog("High score! You're in second! Enter your name:");

            scoreData = (first[0] + ";" + first[1] + "\n" + playerName + ";" + score);
            //acounts for if there is no second place
            if (second != null) {
                //second becomes third :/
                scoreData +=  "\n" + second[0] + ";" + second[1];
            }

        }
        //player is #3
        else if (rank == 3) {
            playerName = JOptionPane.showInputDialog("High score! Third overall, not too shabby! Enter your name:");

            //no need for ifs, cause if they're third then we know first and second do indeed exist
            scoreData = (first[0] + ";" + first[1] + "\n" + second[0] + ";" + second[1] + "\n" + playerName + ";" + score);
        }

        File scoreFile = new File(path);

        //if they are in top 3 scores
        if (scoreData != "") {
            try {
                //add them to the scores! :D
                FileWriter f2 = new FileWriter(scoreFile, false);
                f2.write(scoreData);
                f2.close();
            } catch (IOException e1) {
                e1.printStackTrace();
                }
        }

        //give game over JFrame the cool icon
        ImageIcon img = new ImageIcon("src/Images/icon.png");

        //make game over JFrame
        JFrame gameOver = new JFrame("Oh no!");
        gameOver.setSize(1280, 720);
        gameOver.setLocationRelativeTo(null);
        gameOver.setLayout(null);
        gameOver.getContentPane().setBackground(Color.decode(theme.background));
        gameOver.setIconImage(img.getImage());

        //add a label informing them that they suck
        JLabel youLost = new JLabel();
        youLost.setHorizontalAlignment(SwingConstants.CENTER);
        youLost.setText("<html><div style='text-align: center;'>Game over! Your score was " + score + "!<br><br> Would you like to play again?</html>");
        youLost.setFont(new Font("Bahnschrift", Font.BOLD, 30));
        youLost.setBounds(265, 175, 750, 150);
        youLost .setForeground(Color.decode(theme.button));
        gameOver.getContentPane().add(youLost);

        //indomitable spirit
        JButton yes = new JButton("<html><center>YES</html></center>");
        gameOver.add(yes);
        yes.setBounds(515, 360, 250, 50);
        yes.setFont(new Font("Bahnschrift", Font.BOLD, 18));
        yes.setForeground(Color.decode(theme.background));
        yes.setBackground(Color.decode(theme.button));
        yes.setOpaque(true);
        yes.setBorderPainted(false);
        yes.setFocusPainted(false);

        //what happens when they click the yes button
        yes.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e){
                try {
                    //set play again flag to true so that when the old game window closes, menu doesn't open due to the window listener
                    playAgainFlag = true;
                    //call game starting to start new game
                    GameStarting begin = new GameStarting();
                    begin.beginGame(scheme, hardMode, soundToggle, theme);
                    //close these windows
                    colourWindow.dispatchEvent(new WindowEvent(colourWindow, WindowEvent.WINDOW_CLOSING));
                    gameOver.dispatchEvent(new WindowEvent(gameOver, WindowEvent.WINDOW_CLOSING));

                } catch (Exception e1) {
                    e1.printStackTrace();
                }
            }
        });

        //loser quitter button
        JButton no = new JButton("<html><center>NO THANKS</html></center>");
        gameOver.add(no);
        no.setBounds(515, 460, 250, 50);
        no.setFont(new Font("Bahnschrift", Font.BOLD, 18));
        no.setForeground(Color.decode(theme.background));
        no.setBackground(Color.decode(theme.button));
        no.setOpaque(true);
        no.setBorderPainted(false);
        no.setFocusPainted(false);

        //what happens when they press no
        no.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e){
                //they are not playing again >:(
                playAgainFlag = false;
                //close game & game over windows
                colourWindow.dispatchEvent(new WindowEvent(colourWindow, WindowEvent.WINDOW_CLOSING));
                gameOver.dispatchEvent(new WindowEvent(gameOver, WindowEvent.WINDOW_CLOSING));
                //make main menu update score
                Menu mainMenu = Menu.getInstance();
                mainMenu.updateScore();
            }
        });

        //make game over visible
        gameOver.setVisible(true);

        //reset answer term
        answerTerm = 0;
    }

    //use key listener! they can press keys 1-4 to play the game!
    @Override
    public void keyPressed(KeyEvent e) {
        String pressedKey = String.valueOf(e.getKeyChar());
        if (pressedKey.equals("1")) {
            //just call doClick to simulate button press!
            butColour1.doClick();
        }
        else if (pressedKey.equals("2")) {
            butColour2.doClick();
        }
        else if (pressedKey.equals("3")) {
            butColour3.doClick();
        }
        else if (pressedKey.equals("4")) {
            butColour4.doClick();
        }
    }

//forced to override these cause class implements keylistener, just leave empty
    @Override
    public void keyTyped(KeyEvent e) {
    }

    @Override
    public void keyReleased(KeyEvent e) {
    }

    //this is when they answer a specific number (corresponding to the colour button they chose), it plays the corresponding sound and then calls check answer
    public void answer(int answerValue) {
        SoundUtility beep = new SoundUtility();

        if (answerValue == 1 && soundToggle) {
            beep.playSound(SoundUtility.Sounds.colour1);
        }
        else if (answerValue == 2 && soundToggle) {
            beep.playSound(SoundUtility.Sounds.colour2);
        }
        else if (answerValue == 3 && soundToggle) {
            beep.playSound(SoundUtility.Sounds.colour3);
        }
        else if (soundToggle) {
            beep.playSound(SoundUtility.Sounds.colour4);
        }

        //check the answer
        checkAnswer(answerValue);
    }
}