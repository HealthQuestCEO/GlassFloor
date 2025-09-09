<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Glass Bridge Challenge - Cross to Survive!</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

   body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(180deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
            min-height: 100vh;
            overflow: hidden;
            perspective: 2000px;
        }
    /* City skyline background */
        .cityscape {
            position: fixed;
            bottom: -200px;
            left: 0;
            width: 100%;
            height: 400px;
            background: linear-gradient(to top, #000 0%, #1a1a2e 100%);
            z-index: -1;
        }
       .building {
            position: absolute;
            bottom: 0;
            background: linear-gradient(to top, #111, #222);
            border-top: 2px solid #444;
        }
      /* Bridge Container - positioned lower so question box doesn't cover it */
        .bridge-container {
            position: absolute;
            top: 60%;
            left: 50%;
            transform: translate(-50%, -50%) rotateX(15deg);
            width: 1200px;
            height: 150px;
            z-index: 10;
        }
       /* Entry and exit platforms */
        .platform {
            position: absolute;
            width: 120px;
            height: 150px;
            background: linear-gradient(135deg, #2c3e50, #34495e);
            border: 3px solid #444;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 3rem;
            z-index: 15;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
        }

  .platform.start {
            left: -150px;
            background: linear-gradient(135deg, #27ae60, #2ecc71);
            color: white;
        }

   .platform.end {
            right: -150px;
            background: linear-gradient(135deg, #e74c3c, #c0392b);
            color: white;
        }

  .platform.reached {
            background: linear-gradient(135deg, #f39c12, #e67e22);
            animation: victory 1s ease-in-out infinite alternate;
        }
    @keyframes victory {
            0% { box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5); }
            100% { box-shadow: 0 30px 80px rgba(243, 156, 18, 0.5); }
        }

   /* Glass panels on the bridge */
        .glass-panel {
            position: absolute;
            width: 100px;
            height: 150px;
            background: linear-gradient(135deg, 
                rgba(255, 255, 255, 0.1) 0%, 
                rgba(255, 255, 255, 0.05) 50%, 
                rgba(255, 255, 255, 0.1) 100%);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 8px;
            box-shadow: 
                0 10px 30px rgba(0, 0, 0, 0.3),
                0 0 0 1px rgba(255, 255, 255, 0.1) inset;
            transition: all 0.5s ease;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
            font-weight: bold;
            color: rgba(255, 255, 255, 0.8);
        }

   .glass-panel:hover {
            background: rgba(255, 255, 255, 0.15);
            transform: translateY(-5px);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4);
        }
     .glass-panel.safe {
            border-color: #2ecc71;
            background: linear-gradient(135deg, 
                rgba(46, 204, 113, 0.2) 0%, 
                rgba(46, 204, 113, 0.1) 50%, 
                rgba(46, 204, 113, 0.2) 100%);
            box-shadow: 0 0 20px rgba(46, 204, 113, 0.3);
        }
     .glass-panel.broken {
            animation: shatterPanel 1s ease-out forwards;
            pointer-events: none;
        }
      @keyframes shatterPanel {
            0% { 
                transform: rotateX(0deg) translateY(0px);
                opacity: 1;
            }
            30% { 
                transform: rotateX(15deg) translateY(10px);
                opacity: 0.7;
            }
            100% { 
                transform: rotateX(90deg) translateY(500px);
                opacity: 0;
            }
        }
     /* Player character */
        .player {
            position: absolute;
            width: 60px;
            height: 60px;
            background: linear-gradient(135deg, #e74c3c, #c0392b);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.8rem;
            color: white;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.4);
            z-index: 20;
            transition: all 0.8s cubic-bezier(0.25, 0.46, 0.45, 0.94);
            top: -80px;
            left: -90px;
        }
        .player.moving {
            transition-duration: 1.2s;
        }
       .player.falling {
            animation: playerFall 2s ease-in forwards;
            z-index: 1;
        }
       @keyframes playerFall {
            0% { 
                transform: translateY(0px) rotate(0deg);
                opacity: 1;
            }
            100% { 
                transform: translateY(800px) rotate(720deg);
                opacity: 0;
            }
        }
       /* Question interface */
        .question-interface {
            position: fixed;
            top: 50px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.9);
            backdrop-filter: blur(20px);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 15px;
            padding: 30px;
            max-width: 600px;
            width: 90%;
            color: white;
            text-align: center;
            z-index: 100;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
        }

   
