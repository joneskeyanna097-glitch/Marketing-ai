import React, { useState } from 'react';
import { Check, Lock, Zap, TrendingUp, Users, Lightbulb, ArrowRight, Star, Brain, Sparkles, Loader, Send } from 'lucide-react';

const AIMarketingIntelligenceSaaS = () => {
  const [currentView, setCurrentView] = useState('landing');
  const [userTier, setUserTier] = useState('free');
  const [showComparison, setShowComparison] = useState(false);
  const [openFAQ, setOpenFAQ] = useState(null);
  
  // AI Analysis States
  const [analysisType, setAnalysisType] = useState(null);
  const [inputText, setInputText] = useState('Limited time offer - only 3 seats left!');
  const [aiLoading, setAiLoading] = useState(false);
  const [aiResult, setAiResult] = useState(null);
  const [scanCount, setScanCount] = useState(0);

  const tiers = {
    free: {
      name: 'Starter',
      price: '$0',
      period: 'Forever',
      subtitle: 'AI-powered insights. Zero cost. Maximum learning.',
      description: 'Perfect for getting your first wins',
      cta: 'Get Started Free',
      color: '#4ECDC4',
      features: [
        { name: 'AI Emotional Ad Scanner (basic)', locked: false, description: 'AI analyzes 3 triggers + suggests improvements' },
        { name: 'AI Personality Profiler', locked: false, description: 'AI classifies your audience + messaging tips' },
        { name: '5 AI analyses/month', locked: false, description: 'Limited but real AI power' },
        { name: 'AI Copy Rewriter (1 variation)', locked: true, description: 'Upgrade to unlimited variations' },
        { name: 'AI Strategy Advisor', locked: true, description: 'Personalized recommendations in Creator' },
        { name: 'AI Competitor Analyzer', locked: true, description: 'Real-time competitive intelligence' },
        { name: 'API & Integrations', locked: true, description: 'Available in Premium' }
      ],
      psychology: 'Free AI analysis. Habit formation through intelligent insights.',
      aiFeatures: 3,
      monthlyLimit: 5
    },
    mid: {
      name: 'Creator',
      price: '$29',
      period: '/month',
      subtitle: 'Unlimited AI generation + recommendations.',
      description: 'Trusted by creators and small agencies',
      cta: 'Start 14-Day Free Trial',
      color: '#FFD700',
      badge: 'Most Popular',
      features: [
        { name: 'AI Emotional Ad Scanner (full)', locked: false, description: 'AI detects all 8 psychological triggers' },
        { name: 'AI Copy Rewriter (unlimited)', locked: false, description: 'Generate 5+ copy variations instantly' },
        { name: 'AI Strategy Advisor', locked: false, description: 'Personalized recommendations from Claude' },
        { name: 'AI Personality Profiler', locked: false, description: 'Deep psychographic analysis' },
        { name: 'AI Pricing Optimizer', locked: false, description: 'AI suggests optimal pricing strategy' },
        { name: 'AI Ritual Generator', locked: false, description: 'AI designs customer habit sequences' },
        { name: '500 AI analyses/month', locked: false, description: '100x your Free tier' },
        { name: 'Smart Heatmap Analysis', locked: false, description: 'AI predicts attention patterns' }
      ],
      psychology: 'Unlimited AI generation. Removes all creative friction.',
      aiFeatures: 8,
      monthlyLimit: 500
    },
    premium: {
      name: 'Agency',
      price: '$299',
      period: '/month',
      subtitle: 'Enterprise AI strategy engine + team collaboration.',
      description: 'Built for agencies managing $1M+ in client spend',
      cta: 'Schedule AI Demo',
      color: '#FF6B9D',
      badge: 'Enterprise AI Ready',
      features: [
        { name: 'Unlimited AI Everything', locked: false, description: 'Unlimited analyses, generation, strategy' },
        { name: 'AI Competitor Intelligence Suite', locked: false, description: 'Real-time competitor messaging analysis' },
        { name: 'AI A/B Test Generator', locked: false, description: 'AI generates statistical test variants' },
        { name: 'AI Campaign Orchestrator', locked: false, description: 'AI plans entire customer journey' },
        { name: 'Team Collaboration (unlimited)', locked: false, description: 'AI workspace for entire team' },
        { name: 'Custom AI Models', locked: false, description: 'Fine-tune AI on your brand/industry' },
        { name: 'Advanced Analytics + AI Insights', locked: false, description: 'Predictive ROI modeling' },
        { name: 'Whitelabel with Branded AI', locked: false, description: 'Your branding on AI outputs' }
      ],
      psychology: 'AI as a team member. Scales creative output 10x.',
      aiFeatures: 12,
      monthlyLimit: 'unlimited'
    }
  };

  const aiCapabilities = [
    {
      name: 'AI Emotional Ad Scanner',
      icon: '🔍',
      description: 'Claude analyzes your copy for psychological triggers in real-time',
      tiers: ['free', 'mid', 'premium'],
      id: 'scanner'
    },
    {
      name: 'AI Copy Rewriter',
      icon: '✍️',
      description: 'Generate unlimited copy variations optimized for different personalities',
      tiers: ['mid', 'premium'],
      id: 'rewriter'
    },
    {
      name: 'AI Strategy Advisor',
      icon: '🧠',
      description: 'Personalized recommendations based on your data + market intelligence',
      tiers: ['mid', 'premium'],
      id: 'strategist'
    },
    {
      name: 'AI Pricing Optimizer',
      icon: '💰',
      description: 'Claude suggests pricing psychology strategy + predicts conversion impact',
      tiers: ['mid', 'premium'],
      id: 'pricing'
    },
    {
      name: 'AI Ritual Generator',
      icon: '⚡',
      description: 'Claude designs habit-forming customer touchpoint sequences',
      tiers: ['mid', 'premium'],
      id: 'ritual'
    },
    {
      name: 'AI Competitor Analyzer',
      icon: '🎯',
      description: 'Real-time analysis of competitor messaging + psychological tactics',
      tiers: ['premium'],
      id: 'competitor'
    }
  ];

  const canUseFeature = (feature) => {
    if (userTier === 'free' && feature.tiers.includes('free')) return true;
    if (userTier === 'mid' && (feature.tiers.includes('mid') || feature.tiers.includes('free'))) return true;
    if (userTier === 'premium') return true;
    return false;
  };

  const runAIAnalysis = async (type) => {
    if (!inputText.trim()) return;
    
    // Check free tier limits
    if (userTier === 'free' && scanCount >= tiers.free.monthlyLimit) {
      alert(`Free tier limit reached (${tiers.free.monthlyLimit}/month). Upgrade to Creator for unlimited analyses.`);
      return;
    }

    setAnalysisType(type);
    setAiLoading(true);
    setScanCount(scanCount + 1);

    try {
      // Call Claude API
      const response = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 1000,
          messages: [
            {
              role: "user",
              content: getPromptForAnalysis(type, inputText)
            }
          ]
        })
      });

      if (!response.ok) {
        throw new Error('API call failed');
      }

      const data = await response.json();
      const analysisText = data.content[0].text;

      setAiResult({
        type,
        title: getTitleForAnalysis(type),
        output: analysisText,
        timestamp: new Date().toLocaleTimeString()
      });
    } catch (error) {
      setAiResult({
        type,
        title: 'Analysis Error',
        output: `Error: ${error.message}. Try again or check API credentials.`,
        timestamp: new Date().toLocaleTimeString()
      });
    }

    setAiLoading(false);
  };

  const getPromptForAnalysis = (type, text) => {
    const baseContext = `You are an expert marketing psychologist and behavioral economist. You analyze marketing copy for psychological triggers and emotional hooks. You understand the 8 key emotional triggers: Urgency, Scarcity, Social Proof, Authority, Fear, Value, Identity, Curiosity.

Here's the user's marketing copy to analyze: "${text}"

`;

    const prompts = {
      scanner: baseContext + `Analyze this copy for emotional triggers. Return a structured analysis:

1. TRIGGERS DETECTED: List which of the 8 triggers are present with impact scores (0-10)
2. STRONGEST HOOK: Identify the most powerful emotional trigger
3. MISSING TRIGGERS: Which triggers could strengthen the copy?
4. IMPROVEMENT: Suggest specific copy changes with the triggers they activate
5. EFFECTIVENESS SCORE: Overall persuasiveness (0-100%)
6. RECOMMENDATION: One key change that would increase conversion rate

Format: Clear sections with bullet points. Be specific and actionable.`,

      rewriter: baseContext + `Generate 6 variations of this copy, each optimized for a different buyer archetype:

1. THE OPTIMIZER (rational, ROI-focused): Lead with metrics and efficiency
2. THE CREATOR (design-conscious, trend-aware): Emphasize beauty and innovation
3. THE CAREGIVER (empathetic, community-driven): Show impact on others
4. THE MAVERICK (bold, disruptive): Position as cutting-edge and exclusive
5. THE SAGE (knowledge-seeking): Provide depth and mastery promise
6. THE LOVER (relationship-focused): Emphasize connection and belonging

For each, provide the rewritten copy (2-3 sentences). Make them distinctly different.`,

      strategist: baseContext + `Provide a detailed marketing strategy recommendation. Include:

1. AUDIENCE ANALYSIS: What personality types does this copy appeal to?
2. MESSAGING GAPS: What's missing that would increase appeal?
3. POSITIONING: How should this brand position itself vs competitors?
4. TACTICAL RECOMMENDATIONS: 3-5 specific changes
5. EXPECTED IMPACT: Predicted conversion rate improvement
6. NEXT STEPS: What should the user test first?

Be specific. Include percentages where possible. Focus on psychological levers.`,

      pricing: baseContext + `Analyze the pricing psychology for this offer. Provide:

1. PRICING ANALYSIS: What pricing strategy is implied/suggested?
2. PSYCHOLOGY AT WORK: Which pricing principles are being used?
3. OPTIMIZATION: What price point would maximize revenue?
4. VARIANTS TO TEST:
   - Charm pricing variant ($X.99 vs $X)
   - Premium anchor (higher price to justify quality)
   - Bundle variant (multiple items for bundled price)
5. PREDICTED CONVERSION: Expected conversion rate at each price point
6. RECOMMENDATION: Which variant to test first and why

Format: Clear sections with specific numbers.`,

      ritual: baseContext + `Design a habit-forming customer ritual sequence. Create a monthly/quarterly cadence:

1. RITUAL FRAMEWORK: Overall customer journey (Awareness → Adoption → Advocacy)
2. MONTHLY TOUCHPOINTS: 4 specific rituals with timing
3. EMOTIONAL ARC: How does each touchpoint build loyalty?
4. HABIT TRIGGERS: What makes customers anticipate each ritual?
5. MEASUREMENT: How to track if ritual is building loyalty?
6. FIRST 90 DAYS: Specific sequence to launch first

Format: Like a calendar. Be specific about timing, channel, and psychology.`,

      competitor: baseContext + `Analyze competitor positioning (based on this copy style). Provide:

1. COMPETITOR MESSAGING: What are competitors likely saying?
2. YOUR GAP: What's your differentiation opportunity?
3. PSYCHOLOGY ANALYSIS: What emotional triggers are underused?
4. POSITIONING PIVOT: How to reposition vs competitors?
5. MESSAGING TEMPLATE: Rewritten copy that dominates competition
6. QUICK WIN: One change that creates immediate differentiation

Be strategic and specific.`
    };

    return prompts[type] || baseContext + "Analyze this marketing copy for insights.";
  };

  const getTitleForAnalysis = (type) => {
    const titles = {
      scanner: '🔍 AI Emotional Trigger Analysis',
      rewriter: '✍️ AI-Generated Copy Variations',
      strategist: '🧠 AI Strategy Recommendations',
      pricing: '💰 AI Pricing Optimization',
      ritual: '⚡ AI Customer Ritual Design',
      competitor: '🎯 AI Competitive Analysis'
    };
    return titles[type] || 'AI Analysis Complete';
  };

  const styles = `
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
      background: #0f1419;
      color: #fff;
    }

    .container {
      max-width: 1400px;
      margin: 0 auto;
      padding: 0 20px;
    }

    header {
      padding: 24px 0;
      border-bottom: 1px solid rgba(255, 255, 255, 0.08);
      position: sticky;
      top: 0;
      background: rgba(15, 20, 25, 0.95);
      backdrop-filter: blur(10px);
      z-index: 100;
    }

    .header-content {
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .logo {
      font-size: 1.3rem;
      font-weight: 700;
      background: linear-gradient(135deg, #ffd700, #ffed4e);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .nav {
      display: flex;
      gap: 32px;
      align-items: center;
    }

    .nav button {
      color: #b0b8c1;
      border: none;
      background: none;
      cursor: pointer;
      font-size: 0.95rem;
      transition: color 0.3s;
    }

    .nav button:hover {
      color: #ffd700;
    }

    .cta-button {
      background: linear-gradient(135deg, #ffd700, #ffed4e);
      color: #0f1419;
      padding: 10px 24px;
      border-radius: 8px;
      font-weight: 600;
    }

    .hero {
      padding: 80px 0;
      text-align: center;
    }

    .ai-badge {
      display: inline-block;
      background: rgba(144, 190, 109, 0.15);
      border: 1px solid rgba(144, 190, 109, 0.5);
      color: #90BE6D;
      padding: 8px 16px;
      border-radius: 20px;
      font-size: 0.85rem;
      font-weight: 600;
      margin-bottom: 24px;
    }

    .hero h1 {
      font-size: 3.5rem;
      font-weight: 300;
      letter-spacing: -1px;
      margin-bottom: 16px;
      line-height: 1.2;
    }

    .hero h1 strong {
      font-weight: 700;
      background: linear-gradient(135deg, #ffd700, #ffed4e);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .hero p {
      font-size: 1.2rem;
      color: #b0b8c1;
      margin-bottom: 32px;
      max-width: 800px;
      margin-left: auto;
      margin-right: auto;
      line-height: 1.6;
    }

    .hero-cta {
      display: flex;
      gap: 16px;
      justify-content: center;
      margin-bottom: 60px;
    }

    .btn {
      padding: 14px 32px;
      border-radius: 10px;
      font-weight: 600;
      cursor: pointer;
      border: none;
      font-size: 1rem;
      transition: all 0.3s ease;
      display: inline-flex;
      align-items: center;
      gap: 8px;
    }

    .btn-primary {
      background: linear-gradient(135deg, #ffd700, #ffed4e);
      color: #0f1419;
    }

    .btn-primary:hover {
      transform: translateY(-3px);
      box-shadow: 0 12px 32px rgba(255, 215, 0, 0.3);
    }

    .btn-secondary {
      background: rgba(255, 255, 255, 0.1);
      color: #fff;
      border: 1px solid rgba(255, 255, 255, 0.2);
    }

    .btn-secondary:hover {
      background: rgba(255, 255, 255, 0.15);
      border-color: #ffd700;
    }

    .ai-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
      gap: 24px;
      margin-bottom: 40px;
    }

    .ai-card {
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: 16px;
      padding: 32px;
      transition: all 0.3s ease;
      cursor: pointer;
    }

    .ai-card:hover {
      border-color: rgba(255, 215, 0, 0.3);
      background: rgba(255, 215, 0, 0.05);
      transform: translateY(-8px);
    }

    .ai-icon {
      font-size: 2.5rem;
      margin-bottom: 16px;
    }

    .ai-card h3 {
      font-size: 1.2rem;
      color: #ffd700;
      margin-bottom: 12px;
      font-weight: 600;
    }

    .ai-card p {
      color: #b0b8c1;
      font-size: 0.95rem;
      line-height: 1.6;
    }

    .pricing-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
      gap: 32px;
      margin-bottom: 40px;
    }

    .pricing-card {
      background: linear-gradient(135deg, rgba(255, 255, 255, 0.05) 0%, rgba(255, 255, 255, 0.02) 100%);
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: 16px;
      padding: 40px 32px;
      position: relative;
      transition: all 0.4s ease;
      display: flex;
      flex-direction: column;
    }

    .pricing-card:hover {
      border-color: rgba(255, 215, 0, 0.3);
      transform: translateY(-8px);
    }

    .pricing-card.featured {
      border: 2px solid #ffd700;
      background: linear-gradient(135deg, rgba(255, 215, 0, 0.1) 0%, rgba(255, 215, 0, 0.03) 100%);
      transform: scale(1.05);
    }

    .badge {
      position: absolute;
      top: -12px;
      right: 24px;
      background: #ffd700;
      color: #0f1419;
      padding: 6px 16px;
      border-radius: 20px;
      font-size: 0.85rem;
      font-weight: 700;
    }

    .pricing-name {
      font-size: 1.4rem;
      font-weight: 600;
      margin-bottom: 8px;
    }

    .pricing-price {
      display: flex;
      align-items: baseline;
      gap: 8px;
      margin-bottom: 8px;
    }

    .price-amount {
      font-size: 3.2rem;
      font-weight: 700;
      background: linear-gradient(135deg, #ffd700, #ffed4e);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .pricing-cta {
      width: 100%;
      padding: 12px 24px;
      background: rgba(255, 215, 0, 0.1);
      color: #ffd700;
      border: 1px solid rgba(255, 215, 0, 0.3);
      border-radius: 10px;
      font-weight: 600;
      cursor: pointer;
      margin-bottom: 24px;
      transition: all 0.3s ease;
    }

    .pricing-cta:hover {
      background: #ffd700;
      color: #0f1419;
      border-color: #ffd700;
    }

    .features-list {
      flex: 1;
      margin-bottom: 24px;
    }

    .feature-item {
      display: flex;
      gap: 12px;
      padding: 12px 0;
      border-bottom: 1px solid rgba(255, 255, 255, 0.05);
      font-size: 0.95rem;
      color: #e0e6ec;
    }

    .feature-item.locked {
      opacity: 0.6;
      color: #b0b8c1;
    }

    .demo-section {
      padding: 80px 0;
      background: rgba(144, 190, 109, 0.05);
      border-radius: 20px;
      margin: 60px 0;
    }

    .section-header {
      text-align: center;
      margin-bottom: 60px;
    }

    .section-header h2 {
      font-size: 2.8rem;
      font-weight: 300;
      margin-bottom: 16px;
    }

    .section-header p {
      font-size: 1.1rem;
      color: #b0b8c1;
      max-width: 600px;
      margin: 0 auto;
    }

    .demo-container {
      max-width: 900px;
      margin: 0 auto;
    }

    .feature-buttons {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
      gap: 12px;
      margin-bottom: 24px;
    }

    .feature-btn {
      padding: 12px 16px;
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: 8px;
      color: #b0b8c1;
      cursor: pointer;
      font-weight: 600;
      transition: all 0.3s;
    }

    .feature-btn.active {
      background: rgba(255, 215, 0, 0.2);
      border-color: #ffd700;
      color: #ffd700;
    }

    .feature-btn.locked {
      opacity: 0.5;
      cursor: not-allowed;
    }

    .input-area {
      margin-bottom: 24px;
    }

    .demo-input {
      width: 100%;
      padding: 16px;
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: 12px;
      color: #fff;
      font-size: 1rem;
      margin-bottom: 16px;
      font-family: inherit;
    }

    .demo-input:focus {
      outline: none;
      border-color: #ffd700;
      background: rgba(255, 215, 0, 0.05);
    }

    .demo-output {
      background: rgba(144, 190, 109, 0.1);
      border: 1px solid rgba(144, 190, 109, 0.3);
      border-radius: 12px;
      padding: 24px;
      margin-top: 24px;
      min-height: 100px;
      color: #e0e6ec;
      line-height: 1.6;
      white-space: pre-wrap;
      font-family: 'Courier New', monospace;
      font-size: 0.95rem;
    }

    .scan-counter {
      color: #b0b8c1;
      font-size: 0.9rem;
      margin-bottom: 16px;
    }

    .scan-counter.warning {
      color: #ff9800;
    }

    footer {
      background: rgba(255, 255, 255, 0.02);
      border-top: 1px solid rgba(255, 255, 255, 0.1);
      padding: 60px 0 40px;
      margin-top: 100px;
    }

    .footer-content {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 40px;
      margin-bottom: 40px;
    }

    .footer-section h4 {
      color: #ffd700;
      margin-bottom: 16px;
      font-weight: 600;
    }

    .footer-section a {
      display: block;
      color: #b0b8c1;
      text-decoration: none;
      margin-bottom: 12px;
      font-size: 0.95rem;
      transition: color 0.3s;
    }

    .footer-section a:hover {
      color: #ffd700;
    }

    .footer-bottom {
      text-align: center;
      padding-top: 24px;
      border-top: 1px solid rgba(255, 255, 255, 0.1);
      color: #b0b8c1;
      font-size: 0.9rem;
    }

    .floating-cta {
      position: fixed;
      bottom: 30px;
      right: 30px;
      background: linear-gradient(135deg, #ffd700, #ffed4e);
      color: #0f1419;
      padding: 14px 28px;
      border-radius: 50px;
      font-weight: 600;
      box-shadow: 0 12px 32px rgba(255, 215, 0, 0.3);
      cursor: pointer;
      animation: pulse 2s infinite;
      z-index: 50;
    }

    @keyframes pulse {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.05); }
    }

    .floating-cta:hover {
      transform: scale(1.08);
      animation: none;
    }
  `;

  return (
    <html>
      <head>
        <style>{styles}</style>
      </head>
      <body>
        {/* HEADER */}
        <header>
          <div className="container">
            <div className="header-content">
              <div className="logo">
                <Brain size={24} /> AI Marketing Intelligence
              </div>
              <nav className="nav">
                <button onClick={() => setCurrentView('landing')}>Pricing</button>
                <button onClick={() => setCurrentView('demo')}>Live Demo</button>
                <button className="cta-button" onClick={() => setCurrentView('demo')}>
                  Try Free AI Now
                </button>
              </nav>
            </div>
          </div>
        </header>

        {/* LANDING PAGE */}
        {currentView === 'landing' && (
          <>
            {/* HERO */}
            <section className="hero">
              <div className="container">
                <div className="ai-badge">
                  🤖 Powered by Claude AI + Behavioral Psychology
                </div>
                <h1>
                  AI that understands <strong>why people buy</strong>.<br/>
                  Then helps you say it.
                </h1>
                <p>
                  Scan copy for psychological triggers. Generate variations for different personalities. 
                  Get real-time strategy recommendations.<br/>
                  All powered by Claude's intelligence.
                </p>
                <div className="hero-cta">
                  <button className="btn btn-primary" onClick={() => setCurrentView('demo')}>
                    <Sparkles size={20} /> Try Free AI Analysis
                  </button>
                  <button className="btn btn-secondary">
                    See How It Works
                  </button>
                </div>
              </div>
            </section>

            {/* AI FEATURES */}
            <section style={{ padding: '100px 0' }}>
              <div className="container">
                <div className="section-header">
                  <h2>Claude Inside Your Marketing Stack</h2>
                  <p>Six AI superpowers that understand buyer psychology</p>
                </div>

                <div className="ai-grid">
                  {aiCapabilities.map((feature, i) => (
                    <div key={i} className="ai-card" onClick={() => setCurrentView('demo')}>
                      <div className="ai-icon">{feature.icon}</div>
                      <h3>{feature.name}</h3>
                      <p>{feature.description}</p>
                      <div style={{ marginTop: '16px', fontSize: '0.85rem', color: '#90BE6D' }}>
                        {feature.tiers.length === 1 ? `${feature.tiers[0]} tier only` : 'Multiple tiers available'} →
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            </section>

            {/* PRICING */}
            <section style={{ padding: '100px 0' }}>
              <div className="container">
                <div className="section-header">
                  <h2>Pricing. AI-Powered at Every Level.</h2>
                  <p>Free AI analysis. Creator gets Claude unlimited. Agency gets full enterprise suite.</p>
                </div>

                <div className="pricing-grid">
                  {Object.entries(tiers).map(([key, tier]) => (
                    <div key={key} className={`pricing-card ${key === 'mid' ? 'featured' : ''}`}>
                      {tier.badge && <div className="badge">{tier.badge}</div>}
                      
                      <div>
                        <div className="pricing-name">{tier.name}</div>
                        <div style={{ fontSize: '0.85rem', color: '#90BE6D', marginBottom: '12px', fontWeight: '600' }}>
                          🤖 {tier.aiFeatures} AI Features
                        </div>
                        <div className="pricing-price">
                          <span className="price-amount">{tier.price}</span>
                          <span style={{ color: '#b0b8c1' }}>{tier.period}</span>
                        </div>
                        <div style={{ fontSize: '0.95rem', color: '#b0b8c1', marginBottom: '24px' }}>{tier.description}</div>
                      </div>

                      <button 
                        className="pricing-cta"
                        onClick={() => {
                          setUserTier(key);
                          setCurrentView('demo');
                        }}
                      >
                        {tier.cta} →
                      </button>

                      <div className="features-list">
                        {tier.features.slice(0, 6).map((feature, i) => (
                          <div key={i} className={`feature-item ${feature.locked ? 'locked' : ''}`}>
                            <div style={{ marginTop: '2px' }}>
                              {feature.locked ? <Lock size={18} /> : <Check size={18} color="#90BE6D" />}
                            </div>
                            <div>
                              <div>{feature.name}</div>
                            </div>
                          </div>
                        ))}
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            </section>

            {/* CTA */}
            <section style={{ padding: '80px 0', textAlign: 'center' }}>
              <div className="container">
                <h2 style={{ fontSize: '2.5rem', marginBottom: '24px' }}>Ready? Try Claude AI Right Now.</h2>
                <p style={{ fontSize: '1.1rem', color: '#b0b8c1', marginBottom: '32px' }}>
                  Free tier: 5 AI analyses/month. No credit card. No strings.
                </p>
                <button className="btn btn-primary" style={{ fontSize: '1.05rem', padding: '16px 40px' }} onClick={() => setCurrentView('demo')}>
                  Launch Live Demo <ArrowRight size={20} />
                </button>
              </div>
            </section>
          </>
        )}

        {/* LIVE DEMO */}
        {currentView === 'demo' && (
          <section className="demo-section">
            <div className="container">
              <div className="section-header">
                <h2>Live AI Demo: See Claude in Action</h2>
                <p>Paste your copy. Pick an AI feature. Watch Claude analyze and generate.</p>
              </div>

              <div className="demo-container">
                <div style={{ marginBottom: '24px', padding: '16px', background: 'rgba(255, 215, 0, 0.08)', borderRadius: '12px', border: '1px solid rgba(255, 215, 0, 0.3)' }}>
                  <div style={{ color: '#ffd700', fontWeight: '600', marginBottom: '8px' }}>
                    Current Tier: {tiers[userTier].name}
                  </div>
                  <div style={{ color: '#b0b8c1', fontSize: '0.9rem' }}>
                    {userTier === 'free' && `${tiers.free.monthlyLimit - scanCount} analyses remaining this month`}
                    {userTier === 'mid' && `${tiers.mid.monthlyLimit - scanCount} analyses remaining (upgrade for unlimited)`}
                    {userTier === 'premium' && 'Unlimited analyses'}
                  </div>
                </div>

                <div className="input-area">
                  <label style={{ display: 'block', marginBottom: '8px', color: '#e0e6ec', fontWeight: '600' }}>
                    Paste your marketing copy:
                  </label>
                  <textarea 
                    className="demo-input"
                    value={inputText}
                    onChange={(e) => setInputText(e.target.value)}
                    rows="4"
                    placeholder="Enter ad copy, email subject, headline, or any marketing text..."
                    style={{ fontFamily: 'inherit', resize: 'vertical' }}
                  />
                </div>

                <div>
                  <label style={{ display: 'block', marginBottom: '12px', color: '#e0e6ec', fontWeight: '600' }}>
                    Select AI Feature:
                  </label>
                  <div className="feature-buttons">
                    {aiCapabilities.map((feature) => (
                      <button
                        key={feature.id}
                        className={`feature-btn ${analysisType === feature.id ? 'active' : ''} ${!canUseFeature(feature) ? 'locked' : ''}`}
                        onClick={() => {
                          if (canUseFeature(feature)) {
                            setAnalysisType(feature.id);
                          } else {
                            alert(`Upgrade to ${feature.tiers[0]} tier to use this feature`);
                          }
                        }}
                        disabled={!canUseFeature(feature)}
                      >
                        {feature.icon} {feature.name.split(' ')[1]}
                      </button>
                    ))}
                  </div>
                </div>

                {analysisType && (
                  <div style={{ marginTop: '24px' }}>
                    <button 
                      className="btn btn-primary"
                      onClick={() => runAIAnalysis(analysisType)}
                      disabled={aiLoading}
                      style={{ width: '100%', justifyContent: 'center' }}
                    >
                      {aiLoading ? (
                        <>
                          <Loader size={20} style={{ animation: 'spin 1s linear infinite' }} />
                          Claude is analyzing...
                        </>
                      ) : (
                        <>
                          <Send size={20} />
                          Run AI Analysis
                        </>
                      )}
                    </button>
                  </div>
                )}

                {aiResult && (
                  <div>
                    <div style={{ marginTop: '32px', display: 'flex', justifyContent: 'space-between', alignItems: 'center' }}>
                      <h4 style={{ color: '#ffd700', fontSize: '1.2rem' }}>
                        {aiResult.title}
                      </h4>
                      <span style={{ color: '#b0b8c1', fontSize: '0.85rem' }}>
                        {aiResult.timestamp}
                      </span>
                    </div>
                    <div className="demo-output">
                      {aiResult.output}
                    </div>
                    <div style={{ marginTop: '16px', padding: '12px', background: 'rgba(144, 190, 109, 0.1)', borderRadius: '8px', color: '#90BE6D', fontSize: '0.9rem' }}>
                      💡 This analysis was generated by Claude AI. Ready for more? Upgrade to Creator for unlimited analyses and more features.
                    </div>
                  </div>
                )}

                {userTier === 'free' && (
                  <div style={{ marginTop: '32px', padding: '24px', background: 'rgba(255, 215, 0, 0.08)', borderRadius: '12px', border: '1px solid rgba(255, 215, 0, 0.3)', textAlign: 'center' }}>
                    <h4 style={{ color: '#ffd700', marginBottom: '8px' }}>Ready for Unlimited AI?</h4>
                    <p style={{ color: '#b0b8c1', marginBottom: '16px' }}>
                      Creator tier includes unlimited analyses + all AI features except Competitor Analyzer.
                    </p>
                    <button className="btn btn-primary" onClick={() => setUserTier('mid')}>
                      Upgrade to Creator ($29/month)
                    </button>
                  </div>
                )}
              </div>
            </div>
          </section>
        )}

        {/* FOOTER */}
        <footer>
          <div className="container">
            <div className="footer-content">
              <div className="footer-section">
                <h4>Product</h4>
                <a onClick={() => setCurrentView('demo')}>Try AI Demo</a>
                <a onClick={() => setCurrentView('landing')}>Pricing</a>
                <a href="#">Documentation</a>
              </div>
              <div className="footer-section">
                <h4>Company</h4>
                <a href="#">About</a>
                <a href="#">Blog</a>
                <a href="#">Contact</a>
              </div>
              <div className="footer-section">
                <h4>Legal</h4>
                <a href="#">Privacy</a>
                <a href="#">Terms</a>
              </div>
            </div>
            <div className="footer-bottom">
              <p>© 2025 AI Marketing Intelligence. Built with Claude. Powered by behavioral psychology.</p>
            </div>
          </div>
        </footer>

        {/* FLOATING CTA */}
        <div 
          className="floating-cta"
          onClick={() => setCurrentView('demo')}
        >
          ✨ Try Free AI
        </div>
      </body>
    </html>
  );
};

export default AIMarketingIntelligenceSaaS;

